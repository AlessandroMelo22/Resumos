# `spring-expression`  

### ⚙ Função:  
Fornece o **SpEL (Spring Expression Language)**, uma linguagem de expressões do Spring que permite escrever expressões dinâmicas dentro de configurações e anotações, sem precisar de código Java convencional.  

---

### 🛠 Recursos disponíveis:  
Podemos utilizar essas expressões em diversos casos como por exemplo:

- Em anotações, como @Value(), para definir valores dinâmicos.

Exemplo:
```
@Value("#{2 + 3}")
private int resultado; // resultado = 5
```
---
- Em configurações XML ou classes Java, quando precisamos calcular ou definir valores baseados em condições ou propriedades.

Exemplo:

```
<bean id="calculadora" class="com.exemplo.Calculadora">
    <property name="resultado" value="#{2 * 10}" />
</bean>
```

Exemplo 02:

```
@Configuration
public class AppConfig {

    @Bean
    public String saudacao(@Value("#{ 'Olá, ' + systemProperties['user.name'] }") String mensagem) {
        return mensagem; 
    }
}
// EXEMPLO: "Olá, Alessandro"
```
---
- Para acessar atributos de Beans em tempo de execução, permitindo usar dados e propriedades diretamente nas configurações

Exemplo:

```
@Component
public class Sistema {
    private String nome = "Gestor Financeiro";

    public String getNome() {
        return nome;
    }
}
```
```
@Component
public class InfoService {

    @Value("#{sistema.nome}")
    private String nomeSistema;

    public void exibirNome() {
        System.out.println("Nome do sistema: " + nomeSistema);
    }
}
// SAÍDA: Nome do sistema: Gestor Financeiro
```
---

### ❌Sem o spring-context:  
Sem o `spring-expression`, as configurações no Spring **seriam limitadas a valores fixos**, sem poder incluir lógicas ou expressões dinâmicas diretamente nas anotações e arquivos de configuração. 

