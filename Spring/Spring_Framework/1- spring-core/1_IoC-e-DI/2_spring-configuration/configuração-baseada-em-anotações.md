# Configuração baseada em anotações:


A configuração baseada em anotações trouxe uma forma **declarativa e enxuta de configurar o container IoC**.
Em vez de declarar **manualmente** cada bean (`@Configuration` + `@Bean`), o desenvolvedor pode simplesmente **anotar classes com estereótipos como `@Component`, `@Service`, `@Repository` ou `@Controller`**. 

Combinada ao `@ComponentScan` (_componente da configuração baseada em classes Java_), o Spring passa a **detectar automaticamente essas classes durante a inicialização**, criando os beans de forma automática.
Essa abordagem reduz o código de configuração e aproxima ainda mais a configuração da lógica de negócio.


> [!IMPORTANT]
> **O Spring faz o trabalho de registrar beans automaticamente com base em anotações colocadas nas classes.
O desenvolvedor não precisa declarar cada bean manualmente — basta anotar as classes corretas.**
>
> É o modelo de configuração que prevalece até a **Configuração Automática do Spring Boot (`@SpringBootApplication` + Auto Configuration)**.




**Vantagens**:

✅ Reduz código de configuração manual  
✅ Facilita a criação rápida de projetos  
✅ Mantém a configuração próxima do código de negócio

**Desvantagens**:

❌ Pode gerar menos controle explícito sobre os beans criados  
❌ Dificulta o rastreamento de dependências em sistemas muito grandes



# Exemplo de como trabalhar com configuração baseada em Anotações


## 1️⃣ Criação do nosso POJO:

Para os exemplos, imaginemos que temos um POJO do tipo `Desktop`
```java
public class Desktop implements Computer {

		private String brand;
		private String ram;

    public Desktop() {
        
    }
		public Desktop(String brand, String ram) {
        this.brand = brand;
        this.ram = ram;
    }

    @Override
    public void compile() {
        System.out.println("Compiling using Desktop...");
    }
    
   //Getters e Setters:
}
```
## 2️⃣ Utilizando Anotações de Estereótipo  

O Spring fornece diversas **anotações de estereótipo**, utilizadas para **indicar que uma determinada classe é um candidato a ser gerenciado como um bean dentro do contêiner IoC**.  

**Cada anotação é usada em um contexto específico**, ajudando o Spring (e o próprio desenvolvedor) a entender a função daquela classe na aplicação.

**Exemplos de anotações de estereótipo**

`@Component` → anota uma classe genérica como um bean gerenciado pelo Spring.

`@Service` → usada em classes de regra de negócio ou serviços da aplicação.

`@Repository` → indica uma classe responsável por acesso a dados (DAO ou repositórios).

`@Controller` → usada em aplicações Web MVC, representa um controlador que lida com requisições HTTP.

`@RestController` → uma variação de @Controller usada em APIs REST, combinando @Controller + @ResponseBody.

`@Configuration` → indica uma classe de configuração Java, responsável por declarar beans via métodos @Bean.

`@Entity` → embora pertença ao JPA, e não diretamente ao Spring, também marca classes que representam entidades persistentes no banco de dados.


Ver se compensa colocar o código aqui ou la em baixo
**Exemplo prático**
```java
@Component
public class Desktop implements Computer {

    private String brand;
    private String ram;

    public Desktop() {}

    public Desktop(String brand, String ram) {
        this.brand = brand;
        this.ram = ram;
    }

    @Override
    public void compile() {
        System.out.println("Compiling using Desktop...");
    }

    // Getters e Setters...
}
```



### 🔹Como o Spring descobre essas classes

Para que o Spring possa **criar automaticamente os beans a partir dessas anotações**, é necessário **habilitar o escaneamento de componentes (_component scanning_)**, geralmente feito em uma **classe de configuração**:

```java
@Configuration
@ComponentScan(basePackages = "com.exemplo")
public class AppConfig {
}
 
```


Com isso, o Spring passará a **varrer os pacotes especificados e registrar automaticamente todas as classes anotadas com `@Component` (e suas variações) no contêiner IoC**.

**Exemplo prático**
```java
@Component
public class Desktop implements Computer {

    private String brand;
    private String ram;

    public Desktop() {}

    public Desktop(String brand, String ram) {
        this.brand = brand;
        this.ram = ram;
    }

    @Override
    public void compile() {
        System.out.println("Compiling using Desktop...");
    }

    // Getters e Setters...
}
```

Nesse exemplo, a classe Desktop foi anotada com @Component.
Isso significa que, durante o processo de escaneamento de componentes, o Spring detectará automaticamente essa classe e criará uma instância (bean) dela no contexto da aplicação.

Conectando com a configuração baseada em classe Java

As anotações de estereótipo fazem parte da configuração baseada em anotações, mas dependem da configuração baseada em classe Java para funcionarem.
Ou seja, o escaneamento (@ComponentScan) precisa ser ativado dentro de uma classe @Configuration para que o Spring possa localizar e registrar esses beans automaticamente.
