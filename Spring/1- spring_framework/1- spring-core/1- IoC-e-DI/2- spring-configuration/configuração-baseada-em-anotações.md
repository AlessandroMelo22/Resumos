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

👉 Para que o Spring saiba que a classe deve ser gerenciada por ele, basta anotarmos a mesma com alguma dessas anotações de estereótipo

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
👉 Nesse exemplo, a classe `Desktop` foi anotada com `@Component`. Isso significa que, durante o processo de escaneamento de componentes, o Spring **detectará automaticamente essa classe e criará uma instância (bean) dela no contexto da aplicação**.



## 3️⃣Criando o Container e ativando o Escaneamento dos Componentes:

### 🔹 Criando o Container:

```java
public class Main {
    public static void main(String[] args) {
		//Cria um container ApplicationContext e recebe a classe @Configuration com toda a configuração
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        
		//Recupera o bean
        Desktop desktop01 = applicationContext.getBean(Desktop.class);

		//Usa o bean
		desktop01.compile();
    }
}
```

 👉 Aqui:

- **`AnnotationConfigApplicationContext`** → assim como na configuração baseada em classes Java, na configuração baseada em anotações também utilizamos essa implementação do container `ApplicationContext`, passando também a **classe de configuração da aplicação** (**classe `@Configuration`**).
- **`getBean()`** → método utilizado para recuperar um bean, podendo ser passado apenas a classe do bean (como no exemplo) ou a classe do bean junto com o seu nome. O bean por padrão já possui um nome, sendo ele o nome do método anotado com `@Bean` (no nosso exemplo `createDesktop`).

### 🔹 Ativando o Escaneamento dos Componentes:

Para que o Spring possa **criar automaticamente os beans a partir dessas anotações**, é necessário **habilitar o escaneamento de componentes (*component scanning*)**. 

Como temos uma **classe de configuração** (classe `@Configuration`), anotamos a mesma com `@ComponentScan` e junto passamos o **nome do diretório (package) onde estão as classes que o Spring deverá gerenciar (classes com `@Component` ou suas derivações)**.

```java
@Configuration
@ComponentScan(basePackages = "com.exemplo")
public class AppConfig {
}
```

👉 Com isso, o Spring passará a **varrer os pacotes especificados e registrar automaticamente todas as classes anotadas com `@Component` (e suas variações) no contêiner IoC**.

## **4️⃣ Outras anotações utilizadas na configuração baseada em anotações**

### 🔹 Autowire:

Como vimos anteriormente na **configuração baseada em classe Java**, quando temos um bean que depende de outro bean, podemos utilizar o mecanismo de ***Autowire***, passando a **dependência como parâmetro no construtor do bean**, assim o **Spring fica responsável por injetar essa dependência de forma automática**.

Na configuração baseada em anotações também podemos utilizar o ***Autowire***, sendo possível de algumas formas:

***◽ Autowire* através do campo (atributo)**

Na classe que será **gerenciada pelo Spring** (**classe com `@Component` ou suas derivações**) basta que anotemos o **campo que representa a dependência (ponto de injeção)** com a anotação `@Autowired`

Para a situação, imaginamos que temos a classe `Student` que depende de um `Computer`:

```java
@Component
public class Student {

    private String name;
    @Autowired
    private Computer computer; //ponto de injeção

    public Student(){
    }

    public Student(String name) {
        this.name = name;
        this.computer = computer;
    }

    public void code(){
        System.out.println("Coding...");
        computer.compile();
    }
    
    //Getters e Setters
```

*◽ **Autowire* através do Construtor:**

Como o nome já sugere, para essa abordagem basta **passar a dependência no Construtor da classe que será gerenciada (`@Component` ou derivações), e junto a anotação `@Autowired`**.

```java
@Component
public class Student {

    private String name;
    private Computer computer; 

    public Student(){
    }
		
	@Autowired
    public Student(String name, Computer computer) { //dependência passada no Construtor
        this.name = name;
        this.computer = computer;
    }

    public void code(){
        System.out.println("Coding...");
        computer.compile();
    }
    
    //Getters e Setters
```

***◽ Autowire* através do setter:**

Como o nome da abordagem já sugere basta **passarmos a anotação `@Autowired` no método `setter` da dependência**:

```java
@Component
public class Student {

    private String name;
    private Computer computer; 

    public Student(){
    }
		
    public Student(String name, Computer computer) { 
        this.name = name;
        this.computer = computer;
    }

    public void code(){
        System.out.println("Coding...");
        computer.compile();
    }
    
    public int getName() {
        return name;
    }

    public void setName(String name) {
        this.name= name;
    }

    public Computer getComputer() {
        return computer;
    }

    @Autowired
    public void setComputer(Computer computer) { //injeção através do setter
        this.computer = computer;
    }
```

### 🔹 Primary e Qualifier:

Assim como na configuração com classes Java, a configuração baseada em anotações também pode enfrentar **problemas de ambiguidades** quando **há mais de um *bean* do mesmo tipo**. Para lidar com isso, podemos usar dois mecanismos já conhecidos — **@Primary** e **@Qualifier**— também disponíveis na configuração por classes Java, mas aplicados de forma **um pouco diferente aqui**.

**Exemplo prático**

Para a situação imaginamos que temos além da classe `Desktop`, também temos a classe `Notebook` que também implementa a interface `Computer`, e temos a classe `Student` que depende de um `Computer`:

`Student`

```java
@Component
public class Student {

    private String name;
    @Autowired
    private Computer computer;

    public Student(){
    }

    public Student(String name) {
        this.name = name;
        this.computer = computer;
    }

    public void code(){
        System.out.println("Coding...");
        computer.compile();
    }
    
    //Getters e Setters
}
```

`Desktop`

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

    // Getters e Setters
}
```

`Notebook`

```java
@Component
public class Notebook implements Computer {

    private String brand;
    private String ram;

    public Notebook() {}

    public Notebook(String brand, String ram) {
        this.brand = brand;
        this.ram = ram;
    }

    @Override
    public void compile() {
        System.out.println("Compiling using Notebook...");
    }

    // Getters e Setters
}
```

**`@Primary`**:

Enquanto na configuração com classes Java anotamos o método **`@Bean`** com **`@Primary`**, na configuração baseada em anotações aplicamos **`@Primary`** diretamente sobre a classe gerenciada pelo Spring (anotada com **`@Component`** ou uma de suas derivações), definindo-a como o *bean* padrão.

```java
@Primary
@Component
public class Notebook implements Computer { //Bean definido como padrão e que será usado quando tiver multiplas implementações

    private String brand;
    private String ram;

    public Notebook() {}

    public Notebook(String brand, String ram) {
        this.brand = brand;
        this.ram = ram;
    }

    @Override
    public void compile() {
        System.out.println("Compiling using Notebook...");
    }

    // Getters e Setters
}
```

**`@Qualifier`**:

O `@Qualifier` tem a mesma proposta de utilização, deve ser anotado no **ponto de injeção da dependência** (através do **atributo**, **Construtor** ou **método setter**) junto com o **nome do bean que deverá ser injetado pelo Spring**.

```java
@Component
public class Student {

    private String name;
    @Autowired
    @Qualifier("notebook")
    private Computer computer;

    public Student(){
    }

    public Student(String name) {
        this.name = name;
        this.computer = computer;
    }

    public void code(){
        System.out.println("Coding...");
        computer.compile();
    }
    
    //Getters e Setters
}
```

👉 Aqui:

- **Nome dos beans:**
    - `@Qualifier("notebook")` → por padrão o nome de um bean é basicamente o **nome da classe gerenciada pelo Spring** (classe com `@Component` ou derivações) **trocando apenas a primeira letra maiúscula por minúscula (`Notebook` → `notebook`)**.
    
    - É possível alterar o nome do bean através das anotações que definem a classe como um bean gerenciado pelo Spring (`@Component` ou derivações):
        
        ```java
        @Component("note01")
        public class Notebook implements Computer {
        ```
        
> [!WARNING]
> Caso você tente utilizar o `@Primary` em um bean e passe outro usando o `@Qualifier`, o Spring SEMPRE dará preferência para o `@Qualifier`.
> 

```java
@Component
public class Student {

    private String name;
    
    @Qualifier("notebook") //O Spring sempre dará preferência para o @Qualifier
    @Autowired
    private Computer computer;

	//Construtores
    //Getters e Setters
}
```

```java
@Primary
@Component
public class Desktop implements Computer {
}
```

### 🔹 @Scope e @Value

**`@Scope`**:

Na configuração baseada em anotações - assim como na configuração com classes Java - podemos definir o **Escopo de um bean**.

Para isso basta adicionarmos a anotação `@Scope` na classe que será gerenciada pelo Spring (classe com `@Component` ou derivações):

```java
@Scope("prototype") // outros valores: "request", "session", "application" 
@Component
public class Desktop implements Computer {
}
```

**`@Value`**:

Podemos também passar valores para variáveis usando a anotação `@Value` junto com o valor desejado:

```java
@Component
public class Student {

		@Value("Alessandro")
    private String name;
    
    @Autowired
    private Computer computer;

}
```

Além disso também podemos passar valores que estão dentro de **arquivos de propriedades** (`.properties` ou `.yml`) usando seguinte sintaxe: `@Value("${minha.propriedade}")`:

**arquivo `.properties`**

```java
fieldName= Alessandro
```

```java
@Component
public class Student {

	@Value("${fieldName}") // SAÍDA: Alessandro
    private String name;
    
    @Autowired
    private Computer computer;

}
```
