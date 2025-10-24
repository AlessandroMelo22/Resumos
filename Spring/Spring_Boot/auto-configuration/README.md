# AutoConfiguration  

O **AutoConfiguration** (ou **Configuração Automática**) é um dos **pilares centrais do Spring Boot**.
Ele tem como objetivo **eliminar a necessidade de configurar manualmente diversos aspectos do Spring Framework** — algo que, até então, era feito via:

- XML,
- Classes `@Configuration`,
- Ou uma combinação de `@ComponentScan` + anotações de estereótipo (`@Component`, `@Service`, etc...).

## 1️⃣ O que é o AutoConfiguration?

O AutoConfiguration é um **mecanismo interno do Spring Boot** que **configura automaticamente** vários componentes do Spring **com base no que está disponível no classpath da aplicação**.

> Em outras palavras:  
> O Spring Boot “olha” para o que você adicionou nas dependências e decide sozinho o que precisa ser configurado.

💬 Por exemplo:

Se você adiciona `spring-boot-starter-web`, o Spring Boot automaticamente configura:

- O `DispatcherServlet`,
- O `Tomcat` embutido,
- A infraestrutura do Spring MVC,
- E os beans necessários para lidar com requisições HTTP.

Se você adiciona `spring-boot-starter-data-jpa`, ele configura:

- O `EntityManagerFactory`,
- O `DataSource`,
- O `JpaRepository`,
- E toda a infraestrutura do JPA/Hibernate.

## 2️⃣ Como ele faz isso?

👉 O AutoConfiguration é **habilitado indiretamente pela anotação `@SpringBootApplication`**, que está presente na **classe principal da sua aplicação Spring Boot**.

Essa anotação é, na verdade, um **“atalho” que combina três outras anotações importantes**:
```java
@SpringBootApplication
==>
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

Vamos entender cada uma:  

| Anotação |	Papel |
|----------|--------|
| `@Configuration` |	Indica que a classe define uma **configuração baseada em Java** (igual no Spring tradicional). |
| `@ComponentScan` |	Habilita o **escaneamento de componentes** (descobre beans anotados como `@Component`, `@Service`, etc.). |
| `@EnableAutoConfiguration` |	Ativa o mecanismo de **autoconfiguração do Spring Boot**. |

👉 Portanto, é a anotação `@EnableAutoConfiguration` (embutida em `@SpringBootApplication`) que **dispara todo o processo de autoconfiguração**.


## 3️⃣ Diferenças entre o AutoConfiguration do Spring Boot e a Configuração do Spring puro:

| Spring tradicional |	Spring Boot |
|--------------------|--------------|
| Você diz ao Spring o que criar e configurar. |	O Spring Boot descobre sozinho o que precisa criar e configurar. |
| Você escreve configurações Java ou XML explicitamente. |	O Boot usa convenções e _starters_ para configurar tudo automaticamente. |

Em resumo:

> A configuração baseada em anotações é declarativa e explícita — você define os beans.  
> A autoconfiguração é condicional e automática — o Spring Boot define os beans por você.


## 4️⃣ AutoConfiguration + Configuração manual

Mesmo com o AutoConfiguration ativo, o desenvolvedor pode continuar usando anotações de configuração normalmente (`@Bean`, `@Configuration`, `@Component`, etc.).  

O Spring Boot combina as duas abordagens:

- Primeiro, ele aplica todas as autoconfigurações.
- Depois, ele sobrescreve qualquer bean se você declarar o seu próprio (`@Bean` ou `@Component`).

💬 Assim, você pode:

> “_Deixar o Boot fazer o trabalho pesado, e intervir apenas quando precisar de algo personalizado._”




