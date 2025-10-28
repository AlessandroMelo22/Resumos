# Programação Orientada a Aspectos (AOP)

## 1️⃣ O que é a AOP:

A Programação Orientada a Aspectos (AOP) é um **paradigma** que **complementa a Programação Orientada a Objetos (POO)**.  
Enquanto a POO organiza o código em classes e objetos, a AOP organiza **comportamentos transversais (ou preocupações transversais ou também cross-cutting concerns)** —
ou seja, **funcionalidades que afetam (aparecem) várias partes do sistema**.

🔹 Exemplos de **preocupações transversais**:

- Logs de execução
- Segurança/autenticação
- Transações
- Manipulação de exceções
- Monitoramento de performance

👉 Em vez de duplicar esses códigos em várias classes, a AOP permite que você os **separe em módulos (aspectos)** que o **Spring injeta automaticamente nos pontos certos da aplicação**.


## 2️⃣ Conceitos Fundamentais da AOP


| CONCEITO | DESCRIÇÃO |
|----------|-----------|
| **Aspect** |	Um módulo (classe) que **encapsula uma lógica transversal (ex: um logger, um validador, etc.)** |
| **Join Point** |	Um **ponto de execução do programa** (ex: chamada de método, inicialização de objeto) onde um aspecto pode ser aplicado |
| **Advice** |	O que o aspecto faz — o **código (método) a ser executado no Join Point** |
| **Pointcut** |	Uma **expressão** que define onde (em quais métodos/pacotes/classe) o **advice deve ser aplicado** |
| **Weaving** |	O **processo de "injetar" o aspecto no código (em tempo de compilação, carga ou execução)** |


## 3️⃣ Tipos de Advice no Spring AOP

O Spring oferece vários tipos de Advice, que definem quando o código do aspecto roda:

| Tipo de Advice | Quando é executado | Exemplo |
|----------------|--------------------|---------| 
| **`@Before`** |	Antes da execução do método alvo |	Verificar autenticação antes de um método do controller |
| **`@After`** | Após a execução (sucesso ou falha) |	Liberar recursos |
| **`@AfterReturning`** |	Após execução bem-sucedida | Logar o retorno do método |
| **`@AfterThrowing`** |	Quando o método lança exceção |	Tratar e registrar exceções |
| **`@Around`** |	Antes e depois — controla a execução |	Medir tempo de execução ou modificar o retorno |


### Exemplo Prático de AOP com Spring
```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.seuprojeto.service.*.*(..))")
    public void serviceMethods() {}

    @Before("serviceMethods()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Chamando método: " + joinPoint.getSignature().getName());
    }

    @AfterReturning(pointcut = "serviceMethods()", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        System.out.println("Método retornou: " + result);
    }
}
```

🧠 O que acontece aqui:

- O `@Aspect` define que a classe é um aspecto.
- O `@Pointcut` seleciona todos os métodos dentro de `com.seuprojeto.service`.
- O `@Before` e `@AfterReturning` aplicam a lógica de log **antes e depois da execução desses métodos**.



## 4️⃣ Habilitando AOP no Spring

Adicione a anotação no seu arquivo de configuração principal:
```java
@EnableAspectJAutoProxy
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

