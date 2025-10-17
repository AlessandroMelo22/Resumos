# Configuração baseada em classe Java:

A configuração baseada em classe Java surgiu como uma **alternativa moderna e mais intuitiva à configuração XML**.
Em vez de definir beans em arquivos `.xml`, o desenvolvedor usa classes Java anotadas com `@Configuration` e métodos com `@Bean` para **declarar os componentes que o Spring deve gerenciar**.  

 Essa abordagem trouxe tipagem forte e maior legibilidade, permitindo configurar o container usando a própria linguagem Java.

> [!IMPORTANT]
> **Você ainda controla explicitamente o que o Spring cria, mas de forma mais moderna e enxuta.**
>
> **Essa abordagem normalmente é utilizada junto com a configuração baseada em Anotações (`@ComponetScan` + `@Component`, `@Service`, `@Controller`,...) - elas se completam**.




**Pontos positivos de utilizar esse tipo de configuração:**  
✔ Maior controle da criação e gerenciamento dos beans (possibilidade de passar parâmetros ou settar valores por exemplo)  
✔ Substitui completamente o XML com mais clareza  
✔ Integra-se melhor ao código moderno Java

**Pontos negativos:**  
❌ Ainda exige certo código repetitivo em projetos grandes  
❌ Pode se misturar com código de aplicação se mal estruturado


# Exemplo de como trabalhar com configuração baseada em classe Java

## 1️⃣ Criação do nosso POJO:

Para os exemplos, imaginemos que temos um POJO do tipo `Desktop`:

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
    
  
    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public String getRam() {
        return ram;
    }

    public void setRam(String ram) {
        this.ram = ram;
    }
}
```

## 2️⃣Definição dos metadados na classe `@Configuration`:

Diferente da configuração baseada em XML que utiliza um arquivo `.XML` para instruir o Spring em como gerenciar os beans da aplicação, a configuração baseada em classes Java como o nome já sugere, utiliza **uma classe anotada com `@Configuration` para criar e armazenar as instruções**.

Assim, o Container ao ser inicializado, verifica e aplica as configurações feitas nessa classe.

```java
@Configuration
public class AppConfig {

    @Bean
    public Desktop createDesktop(){ // -> definição de um bean
        return new Desktop();
    }
}
```

👉 Aqui:

- Para criarmos um Bean, utilizamos a anotação `@Bean` em um método que deve retornar um novo objeto (`new`)

## 3️⃣ Criando o Container ApplicationContext e buscando o bean:

```java
public class Main {
    public static void main(String[] args) {
		//Cria um container ApplicationContext e passa a classe @Configuration com toda a configuração
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class); 

		//Recupera o bean
        Desktop desktop01 = applicationContext.getBean(Desktop.class);
				
		//Usa o bean
		desktop01.compile();
    }
}
```

👉 Aqui:

- `AnnotationConfigApplicationContext` → é a implementação do container `ApplicationContext` normalmente utilizado quando trabalhamos com configuração baseada em classe Java
- `getBean()` → método utilizado para recuperar um bean, podendo ser passado apenas a classe do bean (como no exemplo) ou a classe do bean junto com o seu nome. O bean por padrão já possui um nome, sendo ele o nome do método anotado com @Bean (no nosso exemplo `createDesktop`).

```java
@Bean
public Desktop createDesktop(){ // -> O nome do método é o nome padrão do bean
    return new Desktop();
}
```

```java
//Recupera o bean
Desktop desktop01 = applicationContext.getBean("createDesktop", Desktop.class); //-> passando o nome e o tipo do bean
```

## 4️⃣ Outras configurações que podemos fazer dentro da classe `@Configuration`

### 🔹Nome dos beans:

A partir da anotação `@Bean` podemos **alterar o nome do bean, através do atributo `name`**:

```java
@Configuration
public class AppConfig {

    @Bean(name = "desktop01")
    public Desktop createDesktop(){
        return new Desktop();
    }
}
```

Além de atribuir apenas um nome para o bean, podemos também **atribuir múltiplos nomes para um mesmo bean**:

```java
@Bean(name = {"desktop01", "dt01", "deskt01"})
public Desktop createDesktop(){
    return new Desktop();
}
```

### 🔹Escopo:

Como vimos na configuração baseada em XML, podemos alterar o escopo de um bean (sendo ele `Singleton` por padrão), na configuração baseada em classe Java também é possível através da anotação `@Scope`. 

Então utilizamos a anotação `@Scope` junto com o valor que desejamos (`prototype`, `request`, `session`, `aplication`, `websocket`):

```java
@Bean
@Scope("prototype")
public Desktop createDesktop(){
    return new Desktop();
}
```

### 🔹Autowire:

Quando temos um bean que depende de outro bean, devemos nos certificar de fornecer essa dependência, e quando trabalhamos com a configuração baseada em classes Java, uma das formas de fazer isso é através do Construtor, passando a dependência como um parâmetro:

Classe `Student` que depende de um objeto do tipo `Computer`:

```java
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
    
    //Getters e Setters
```

```java
@Configuration
public class AppConfig {

    @Bean(name = {"student01", "std01"})
    public Student createStudent(Computer computer){

        Student student= new Student("Alessandro", computer);
        return student;
    }

    @Bean(name = {"desktop01", "dt01", "deskt01"})
    public Desktop createDesktop(){
        return new Desktop();
    }
}
```

Com isso o Spring realiza o *autowire* de forma automática.

### 🔹Primary e Qualifier:

Imaginemos o caso de termos dois beans do mesmo tipo, por exemplo: `Desktop` e `Notebook`, ambos implementam a Interface `Computer`, e o bean `Student` depende de um bean do tipo `Computer`:

```java
@Configuration
public class AppConfig {

    @Bean(name = {"student01", "std01"})
    public Student createStudent(Computer computer){

        Student student= new Student("Alessandro", computer);
        return student;
    }

    @Bean(name = {"desktop01", "dt01", "deskt01"})
    public Desktop createDesktop(){
        return new Desktop();
    }

    @Bean(name = {"notebook01", "ntbk01", "note01"})
    public Notebook createNotebook(){
        return new Notebook();
    }
}
```

Ao tentar iniciar a aplicação, será lançada uma Exception de ambiguidade:

```diff
- No qualifying bean of type 'com.alessandromelo.interfaces.Computer' available: expected single matching bean but found 2: desktop01,notebook01
```

Quando trabalhamos com XML tínhamos duas maneiras de resolver esse problema: definindo explicitamente qual bean queremos utilizar (usando a tag `<property>` junto com atributo `ref` ou algo do tipo), ou utilizando o atributo `primary` no bean que será utilizado.

Trabalhando com configuração baseada em classes Java podemos usar duas anotações `@Primary` e `@Qualifier`:

**`@Primary`**

Anotamos no método criador do bean indicando que aquele bean é o bean padrão quando houver vários beans do mesmo tipo:

```java
	@Bean(name = {"student01", "std01"})
    public Student createStudent(Computer computer){

        Student student= new Student("Alessandro", computer);
        return student;
    }

    @Bean(name = {"desktop01", "dt01", "deskt01"}) //-> Bean definido como "padrão" que será utilizado
    @Primary
    public Desktop createDesktop(){
        return new Desktop();
    }

    @Bean(name = {"notebook01", "ntbk01", "note01"})
    public Notebook createNotebook(){
        return new Notebook();
    }
```

**`@Qualifier`**

Anotamos o **ponto de injeção do bean**, passando o nome do bean (da implementação) que deverá ser injetada. Basicamente definimos explicitamente **qual implementação do bean deverá ser injetado**:
```java
	@Bean(name = {"student01", "std01"})
    public Student createStudent(@Qualifier("notebook01") Computer computer){ //Anotando o ponto de injeção com @Qualifier e passando o nome da implementação
        Student student= new Student("Alessandro", computer);
        return student;
    }

    @Bean(name = {"desktop01", "dt01", "deskt01"}) 
    public Desktop createDesktop(){
        return new Desktop();
    }

    @Bean(name = {"notebook01", "ntbk01", "note01"}) //-> Bean que será utilizado
    public Notebook createNotebook(){
        return new Notebook();
    }
```


