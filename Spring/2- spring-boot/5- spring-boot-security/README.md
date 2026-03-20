# Spring Security (Configuração, Filtros e Segurança Web)


## Introdução

Após entender o fluxo de autenticação com banco de dados, o próximo passo é aprender a **configurar e customizar** o comportamento do Spring Security. Nesta seção veremos como o framework intercepta requisições, como proteger sua aplicação contra ataques como CSRF, a diferença entre aplicações stateful e stateless, e como usar o `HttpSecurity` para montar sua própria cadeia de segurança.

---
<details>
  <summary><h2>1. Primeiros Passos — Adicionando a Dependência</h2></summary>
  
Para usar o Spring Security, basta adicionar a dependência ao `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Só de adicionar essa dependência, **sem nenhuma configuração extra**, o Spring Security já:

- Protege **todos os endpoints** da aplicação
- Exibe uma **tela de login automática** ao tentar acessar qualquer rota
- Gera um usuário padrão `user` com uma senha aleatória impressa no console a cada inicialização

Por exemplo, ao tentar acessar `localhost:8080/greet` sem estar autenticado, você será redirecionado para a tela de login. A senha gerada aparece no console assim:

```
Using generated security password: 3f4a1b2c-...
```

Além da tela de login, o Spring Security também disponibiliza automaticamente uma tela de logout em `/logout`.

### Definindo usuário fixo no `application.properties`

Para evitar a senha aleatória durante o desenvolvimento, você pode fixar um usuário:

```
spring.security.user.name=Alessandro
spring.security.user.password=123456
```

> ⚠️ Isso é apenas para desenvolvimento. Em produção, os usuários devem vir do banco de dados.
>
  
</details>

---





<details>
  <summary><h2>2. Spring Security Filters e o Filter Chain</h2></summary>

  O Spring Security funciona como uma **cadeia de filtros** (Filter Chain) que intercepta cada requisição HTTP **antes** que ela chegue aos seus controllers.

```
Requisição HTTP
  → FilterChainProxy
      → CsrfFilter
      → UsernamePasswordAuthenticationFilter
      → BasicAuthenticationFilter
      → AuthorizationFilter
      → ...
  → DispatcherServlet
  → Controller
```

Os filtros são executados em uma **ordem específica e bem definida**. Embora seja tecnicamente possível alterar essa ordem, isso **não é recomendado**, pois pode introduzir vulnerabilidades ou comportamentos inesperados.

Cada filtro tem uma responsabilidade clara — um cuida do CSRF, outro da autenticação por formulário, outro da autorização. Juntos, eles formam a barreira de segurança da sua aplicação.
</details>

---



<details>
  <summary><h2>3. CSRF — Cross-Site Request Forgery</h2></summary>

  ### O que é?

CSRF é um ataque onde um site malicioso induz um usuário autenticado a realizar ações não desejadas em outra aplicação — sem que ele saiba.

**O problema sem proteção:**

```
1. Usuário faz login em meubanco.com → navegador salva o cookie de sessão
2. Usuário abre site-malicioso.com em outra aba
3. Site malicioso envia silenciosamente um POST para meubanco.com/transferir
4. O navegador anexa automaticamente o cookie de sessão na requisição
5. O servidor não distingue a requisição legítima da maliciosa ✅ ataque bem-sucedido
```

**A solução com Spring Security:**

O Spring Security gera um **token CSRF único por sessão**. Esse token deve ser enviado em toda requisição que modifica estado (POST, PUT, DELETE, PATCH). Como o site malicioso não tem acesso a esse token, a requisição é bloqueada.

```
Requisição POST sem token  →  CsrfFilter  →  403 Forbidden ❌
Requisição POST com token  →  CsrfFilter  →  segue o fluxo  ✅
```

### Por que só POST, PUT, DELETE?

Métodos como `GET`, `HEAD` e `OPTIONS` são considerados **seguros** — por convenção, não devem modificar dados. Por isso não precisam de proteção CSRF. Já `POST`, `PUT`, `DELETE` e `PATCH` criam, atualizam ou deletam dados, então exigem o token.

### Obtendo o token CSRF

Você pode expor um endpoint para recuperar o token atual:

```java
@GetMapping("/csrf-token")
public CsrfToken getCsrfToken(HttpServletRequest request) {
    return (CsrfToken) request.getAttribute("_csrf");
}
```

Resposta:

```json
{
  "headerName": "X-CSRF-TOKEN",
  "parameterName": "_csrf",
  "token": "M2t49FlrFs_4NCbAKiBJ..."
}
```

Com o token em mãos, basta incluí-lo no header `X-CSRF-TOKEN` das requisições POST, PUT e DELETE.

### Bloqueando acesso ao Session ID por terceiros

Para reforçar ainda mais a segurança, você pode impedir que o cookie de sessão seja enviado em requisições cross-site:

```
server.servlet.session.cookie.same-site=strict
```

O valor `strict` faz com que o cookie só seja enviado em requisições originadas **do mesmo site**, bloqueando completamente o vetor de ataque do CSRF.
</details>






---
<details>
<summary><h2>4. Stateful vs Stateless</h2></summary>

  Entender essa diferença é essencial para decidir como configurar a segurança da sua aplicação.

### 🔸 Stateful (com estado)

O servidor **lembra** do usuário entre requisições, armazenando uma sessão na memória.

```
1. Login → servidor valida as credenciais
2. Servidor cria uma Sessão na memória com os dados do usuário
3. Servidor envia o SessionID para o cliente via Cookie
4. Nas próximas requisições, o navegador envia o Cookie automaticamente
5. Servidor busca a sessão pelo SessionID e identifica o usuário
```

| Conceito | O que é |
| --- | --- |
| **Sessão** | Objeto no servidor que guarda os dados do usuário entre requisições |
| **Cookie** | Armazena o `SessionID` no navegador e é enviado automaticamente em cada requisição |

**Quando usar:** aplicações web tradicionais com frontend renderizado no servidor (Thymeleaf, JSP, etc).

---

### 🔹 Stateless (sem estado)

O servidor **não guarda nada** entre requisições. Cada requisição carrega todas as informações necessárias para ser autenticada.

```
1. Login → servidor valida as credenciais
2. Servidor gera um token JWT assinado digitalmente com os dados do usuário
3. Token é enviado ao cliente (sem salvar nada no servidor)
4. Cliente armazena o token (localStorage ou cookie)
5. Nas próximas requisições, cliente envia o token no header Authorization
6. Servidor valida a assinatura do token — sem consultar banco ou sessão
```

**Vantagens:**

- Escalável horizontalmente (qualquer instância pode validar o token)
- Sem risco de CSRF — não usa cookies de sessão
- Ideal para APIs REST e microsserviços

**Quando usar:** APIs REST consumidas por SPAs (React, Angular, Vue) ou aplicativos mobile.

> 💡 **Por que Stateless não sofre CSRF?**
O ataque CSRF explora o fato de o navegador enviar cookies automaticamente. Em uma aplicação stateless com JWT, o token é enviado manualmente no header `Authorization` pelo código do cliente — o navegador não faz isso sozinho, então o site malicioso não consegue replicar a requisição.
>
</details>


 

---
<details>
<summary><h2>5. Customizando o Spring Security</h2></summary>

  Para customizar o comportamento padrão, criamos uma classe de configuração:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http.build();
    }
}
```

- `@Configuration` — indica ao Spring que essa classe contém definições de Beans
- `@EnableWebSecurity` — ativa a infraestrutura de segurança web, registra o `FilterChainProxy` e importa as configurações internas do Spring Security
- `SecurityFilterChain` — representa a cadeia de filtros que cada requisição vai percorrer
- `HttpSecurity` — um builder fluente que permite configurar toda a cadeia
</details>




---


<details>
<summary><h2>6. Principais métodos do `HttpSecurity`</h2></summary>

  ### 6.1 — Autorização de Requisições

```java
http.authorizeHttpRequests(auth -> auth
    .requestMatchers("/public/**").permitAll()
    .requestMatchers("/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
);
```

| Método | O que faz |
| --- | --- |
| `requestMatchers(...)` | Seleciona as URLs que receberão a regra definida em seguida |
| `anyRequest()` | Define uma regra para todas as URLs não cobertas anteriormente |
| `permitAll()` | Permite acesso sem autenticação |
| `authenticated()` | Exige que o usuário esteja autenticado |
| `hasRole("X")` | Exige uma role específica (ex: `"ADMIN"`) |
| `hasAuthority("X")` | Exige uma authority específica (mais granular que role) |
| `denyAll()` | Bloqueia o acesso completamente |

> ⚠️ A ordem das regras importa. O Spring usa a **primeira que casar** com a URL. Sempre coloque regras mais específicas antes de `anyRequest()`.
> 

---

### 6.2 — Autenticação

**Formulário (stateful):**

```java
http.formLogin(form -> form
    .loginPage("/login")
    .defaultSuccessUrl("/home")
);
```

**HTTP Basic (APIs simples):**

```java
http.httpBasic(Customizer.withDefaults());
```

**OAuth2 / Login Social:**

```java
http.oauth2Login(oauth -> oauth
    .loginPage("/login")
    .defaultSuccessUrl("/home")
);
```

| Método | Quando usar |
| --- | --- |
| `formLogin` | Aplicações web tradicionais com tela de login própria |
| `httpBasic` | APIs internas, testes, integrações simples |
| `oauth2Login` | Login com Google, GitHub, Azure AD, etc |

---

### 6.3 — CSRF

```java
// Desabilitar (comum em APIs stateless com JWT)
http.csrf(csrf -> csrf.disable());

// Configurar repositório de token (stateful com frontend SPA)
http.csrf(csrf -> csrf
    .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
);
```

> 💡 Se sua aplicação é **stateless com JWT**, desabilitar o CSRF é correto e seguro — o ataque não se aplica nesse cenário.
> 

---

### 6.4 — Gerenciamento de Sessão

```java
http.sessionManagement(session -> session
    .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
);
```

| Policy | Comportamento |
| --- | --- |
| `STATELESS` | Nunca cria sessão — ideal para APIs com JWT |
| `IF_REQUIRED` | Cria sessão apenas quando necessário (padrão) |
| `ALWAYS` | Sempre cria uma sessão |
| `NEVER` | Não cria sessão, mas usa uma existente se houver |

---

### 6.5 — CORS

CORS (Cross-Origin Resource Sharing) controla quais origens externas podem fazer requisições para sua API. É essencial quando seu frontend e backend estão em domínios diferentes.

```java
http.cors(Customizer.withDefaults());
```

Para customizar as origens permitidas:

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("https://meufront.com"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
}
```

---

### 6.6 — Filtros Personalizados

Você pode inserir seus próprios filtros na cadeia, algo essencial ao implementar JWT:

```java
// Inserir antes de um filtro existente
http.addFilterBefore(
    new JwtAuthenticationFilter(),
    UsernamePasswordAuthenticationFilter.class
);
```

| Método | Posição do filtro inserido |
| --- | --- |
| `addFilterBefore(filtro, Referencia.class)` | Imediatamente antes do filtro de referência |
| `addFilterAfter(filtro, Referencia.class)` | Imediatamente depois do filtro de referência |
| `addFilterAt(filtro, Referencia.class)` | Na mesma posição do filtro de referência |
| `addFilter(filtro)` | Na posição padrão definida pelo Spring |

---

### 6.7 — Tratamento de Exceções

Permite customizar as respostas HTTP para erros de segurança:

```java
http.exceptionHandling(ex -> ex
    .authenticationEntryPoint(new CustomAuthenticationEntryPoint())
    .accessDeniedHandler(new CustomAccessDeniedHandler())
);
```

| Método | Quando é acionado | Status HTTP padrão |
| --- | --- | --- |
| `authenticationEntryPoint` | Usuário não autenticado tenta acessar recurso protegido | `401 Unauthorized` |
| `accessDeniedHandler` | Usuário autenticado, mas sem permissão suficiente | `403 Forbidden` |

---

### 6.8 — Logout

```java
http.logout(logout -> logout
    .logoutUrl("/logout")
    .logoutSuccessUrl("/login?logout")
    .deleteCookies("JSESSIONID")
    .invalidateHttpSession(true)
);
```

| Método | O que faz |
| --- | --- |
| `logoutUrl(url)` | Define o endpoint que aciona o logout (requisição POST por padrão) |
| `logoutSuccessUrl(url)` | Redireciona para essa URL após logout bem-sucedido |
| `deleteCookies("JSESSIONID")` | Remove o cookie de sessão do navegador |
| `invalidateHttpSession(true)` | Destrói a sessão no servidor |
</details>


---


<details>
<summary><h2>7. Autenticação com Usuários em memória (para testes e desenvolvimento)</h2></summary>

Você pode definir múltiplos usuários diretamente no código, sem banco de dados. Ao criar esse Bean, o Spring ignora as configurações do `application.properties`:

```java
@Bean
public UserDetailsService userDetailsService() {

    UserDetails user = User
        .withDefaultPasswordEncoder()
        .username("lelek")
        .password("l3l3k")
        .roles("USER")
        .build();

    UserDetails admin = User
        .withDefaultPasswordEncoder()
        .username("tobias")
        .password("6769")
        .roles("ADMIN")
        .build();

    return new InMemoryUserDetailsManager(user, admin);
}
```

> ⚠️ `withDefaultPasswordEncoder()` é marcado como deprecated pois não é seguro para produção. Use apenas para testes locais. Em produção, os usuários devem vir do banco de dados com senhas encodadas via `BCryptPasswordEncoder`.
>
</details>


---
<details>
<summary><h2>8. Autenticação com Usuários do Banco de Dados</h2></summary>
  
  <details>
  <summary><h2>8.1 Visão Geral do Fluxo</h2></summary>
Quando um usuário tenta se autenticar, o Spring Security executa a seguinte sequência:

```
Requisição HTTP
  → AuthenticationFilter
  → AuthenticationManager
  → DaoAuthenticationProvider
  → UserDetailsService
  → Banco de Dados
  → UserDetails
  → Token de Autenticação
  → SecurityContext
```

Cada componente tem uma responsabilidade bem definida nessa cadeia. Entender o papel de cada um é o fundamento para trabalhar com Spring Security no dia a dia.
    
  </details>





  <details>
  <summary><h2>8.2 Os Componentes Principais</h2></summary>
    
### `DaoAuthenticationProvider`

O nome pode confundir, mas "Dao" vem de *Data Access Object* — uma referência a padrões de acesso a dados, não ao banco em si. Esse componente é o **orquestrador da autenticação**. Ele:

- Recebe o username e password da requisição
- Usa o `UserDetailsService` para buscar o usuário
- Compara a senha recebida com a senha armazenada via `PasswordEncoder`
- Lança exceção se as credenciais forem inválidas

Para que o Spring Security saiba qual `UserDetailsService` usar e como validar senhas, precisamos registrar o `DaoAuthenticationProvider` explicitamente como um Bean. Por padrão, o framework configura um internamente com valores básicos — ao declarar o Bean, você assume o controle e conecta os componentes da sua aplicação.

```java
@Bean
public AuthenticationProvider authenticationProvider() {
    DaoAuthenticationProvider provider = new DaoAuthenticationProvider();
    provider.setUserDetailsService(userDetailsService); // sua implementação
    provider.setPasswordEncoder(passwordEncoder());     // ex: BCryptPasswordEncoder
    return provider;
}
```

> 💡 O tipo de retorno é `AuthenticationProvider` (interface), não `DaoAuthenticationProvider` (implementação). Isso segue o princípio de programar para interfaces — o Spring Security trabalha com qualquer provedor que implemente essa interface, o que facilita trocar a implementação no futuro se necessário.
> 

---

### `UserDetailsService`

É a **ponte entre o Spring Security e o seu banco de dados**. O Spring Security não conhece sua estrutura de tabelas, seu ORM ou seu repositório — ele apenas sabe que pode chamar esse método e espera receber um `UserDetails` de volta.

|  |  |
| --- | --- |
| **Interface** | `UserDetailsService` |
| **Único método** | `loadUserByUsername(String username)` |
| **Retorno** | `UserDetails` (ou lança `UsernameNotFoundException`) |

Implementação típica:

```java
@Service
public class MyUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username)
                                  throws UsernameNotFoundException {
        return userRepository.findByUsername(username)
            .orElseThrow(() ->
                new UsernameNotFoundException("Usuário não encontrado"));
    }
}
```

---

### `UserDetails`

É o **contrato que o Spring Security entende** para representar um usuário autenticado. Sua entidade de domínio precisa "falar a língua" do Spring Security implementando essa interface.

Métodos obrigatórios da interface:

- `getUsername()` — retorna o identificador único do usuário
- `getPassword()` — retorna a senha (deve estar encodada)
- `getAuthorities()` — retorna as permissões/roles do usuário
- `isAccountNonExpired()`, `isAccountNonLocked()`, `isCredentialsNonExpired()`, `isEnabled()` — controles de acesso

---

### Duas Abordagens para `UserDetails`

### Opção A — Entidade implementa `UserDetails` diretamente

Mais simples, mas mistura lógica de domínio com lógica de segurança:

```java
@Entity
public class User implements UserDetails {
    private String username;
    private String password;
    private String role;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority(role));
    }
    // ...outros métodos
}
```

### Opção B — Classe separada UserPrincipal (recomendada)

Separa responsabilidades. Sua entidade fica limpa, e a classe `UserPrincipal` cuida da autenticação:

```java
public class UserPrincipal implements UserDetails {

    private final User user; // sua entidade de domínio

    public UserPrincipal(User user) { this.user = user; }

    @Override
    public String getUsername() { return user.getEmail(); }

    @Override
    public String getPassword() { return user.getPasswordHash(); }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return user.getRoles().stream()
            .map(r -> new SimpleGrantedAuthority(r.getName()))
            .collect(Collectors.toList());
    }
    // ...
}
```

> **Qual usar?**
> 
> - Projetos simples ou prototipagem → **Opção A** (menos código)
> - Projetos de produção → **Opção B** (melhor separação de responsabilidades)
> - A Opção B também facilita testes unitários, pois isola a lógica de autenticação
    
  </details>

  <details>
  <summary><h2>8.3 PasswordEncoder</h2></summary>

### Por que é tão importante?

**Nunca salve uma senha em texto puro no banco de dados.** Se o banco vazar, todas as senhas ficam expostas imediatamente. O `PasswordEncoder` resolve isso através de **hashing** — uma função de mão única que transforma a senha em uma string embaralhada sem possibilidade de reversão.

Na autenticação, em vez de "*descriptografar*" a senha salva, o Spring aplica o mesmo hash na senha digitada e **compara os dois resultados**:

```
Cadastro:  "minhasenha123"  →  bcrypt  →  "$2a$10$xK8..."  (salvo no banco)
Login:     "minhasenha123"  →  bcrypt  →  "$2a$10$xK8..."  ✅ bate!
Login:     "senhaerrada"    →  bcrypt  →  "$2a$10$zzZ..."  ❌ não bate!
```

---

### BCryptPasswordEncoder

O Spring Security recomenda o **BCrypt**, que tem vantagens sobre hashes simples como MD5 ou SHA-256:

- **Salt automático** — mistura um valor aleatório à senha antes de hashear, então duas pessoas com a mesma senha terão hashes diferentes no banco. Isso impede ataques de *rainbow table*.
- **Custo configurável** — o "strength" (padrão 10) controla o processamento necessário. Quanto maior, mais lento e mais difícil de quebrar por força bruta.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder(); // strength padrão = 10
    // ou: new BCryptPasswordEncoder(12) para mais segurança
}
```

---

### Como ele se encaixa no fluxo

O `DaoAuthenticationProvider` usa o `PasswordEncoder` internamente na comparação:

```
Login do usuário
  → DaoAuthenticationProvider recebe "minhasenha123"
  → chama UserDetailsService.loadUserByUsername()
  → obtém o UserDetails com o hash "$2a$10$xK8..." do banco
  → chama passwordEncoder.matches("minhasenha123", "$2a$10$xK8...")
  → se true  → autenticado ✅
  → se false → BadCredentialsException ❌
```

---

### Encodando no cadastro

Na hora de cadastrar um usuário, você precisa encodar a senha **manualmente** antes de salvar:

```java
@Service
public class UserService {

    @Autowired
    private PasswordEncoder passwordEncoder;

    @Autowired
    private UserRepository userRepository;

    public void cadastrar(String username, String rawPassword) {
        User user = new User();
        user.setUsername(username);
        user.setPassword(passwordEncoder.encode(rawPassword)); // encode aqui!
        userRepository.save(user);
    }
}
```

> ⚠️ **Erro clássico de iniciante:** salvar a senha sem encodar no cadastro e só configurar o encoder na autenticação. O login vai falhar sempre, porque o Spring vai comparar o hash com texto puro e nunca vai bater.
>
  </details>
  
</details>


