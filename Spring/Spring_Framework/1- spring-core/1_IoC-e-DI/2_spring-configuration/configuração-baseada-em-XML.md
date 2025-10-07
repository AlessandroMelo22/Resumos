# Configuração baseada em XML

## 1️⃣ Criação do nosso POJO:

Como primeiro passo, devemos criar nosso classe POJO, então suponhamos que temos uma classe `Customer`:

```java
public class Customer{

    private String name;
    private int age;
		
    private Address address;


    public Customer(){	
    }

    public Customer(String name, int age, Address address){
        this.name= name;
        this.age= age;
        this.address= address;
    }
		
		
    public int getName() {
        return name;
    }
    public void setName(String name) {
        this.name= name;
    }

		public int getAge() {
        return age;
    }
	  public void setAge(int age) {
        this.age= age;
    }
    
    
    public int getAddress() {
        return address;
    }
	  public void setAddress(Address address) {
        this.address= address;
    }
    
    public void toString(){
		    System.out.println("Name: " + this.name + " | Age: " + this.age + " | Address: " + this.address);
    }
    
}
```



## 2️⃣ Definição dos metadados em XML:

Agora precisamos **instruir** o Spring, mais precisamente o Container em **como criar e gerenciar esse POJO**. Isso é feito no **arquivo de configuração XML** (geralmente `applicationContext.xml`)**, 
que deve ser criado dentro de um diretório `/main/resources`**.

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Definição de um Bean -->
    <bean id="customer01" class="com.exemplo.Customer">
        
    </bean>

</beans>
```

👉 Aqui estamos dizendo ao Spring:

- **`id="customer01"`** → nome único para identificar o *bean* dentro do container. Não é obrigatório, mas serve para identificar um bean, quando tiver mais de um *bean* da mesma classe.
- **`class="com.exemplo.Customer"`** → classe que deve ser instanciada.



## 3️⃣ Criação do Container `ApplicationContext`:

Depois de criar as instruções para o Container, podemos criar um Container, no caso o `ApplicationContext`:

```java
public class MainApp {
    public static void main(String[] args) {
        // Cria um ApplicationContext e carrega a configuração do arquivo .XML
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        // Recupera o Bean
        Customer customer = (Customer) context.getBean("customer01");
        
        // Usa o Bean
        customer.toString();
    }
}
```

👉 Passos que acontecem internamente:

- O `ClassPathXmlApplicationContext` é uma das várias implementações disponíveis para a Interface `ApplicationContext`, sendo essa normalmente utilizada quando trabalhamos com a **configuração baseada em XML**.
  Junto com a criação do Container também é passado o **nome do arquivo .XML que contém as instruções (no caso do exemplo “`applicationContext.xml`”)**;
- O Spring cria uma instância de `Customer`.
- O *bean* é registrado no contexto.
- Quando chamamos `context.getBean("customer01")`, o Spring devolve a instância gerenciada.



## 4️⃣ Outros metadados de configuração que podemos definir no arquivo .XML:


### 🔹Escopo dos Beans:

Por padrão, os beans são **`singleton`** (uma única instância). Mas no XML podemos mudar o escopo:

```xml
<bean id="customerPrototype" class="com.exemplo.Customer" scope="prototype"/>

</bean>
```

- **`singleton`** → Fornece apenas uma instância de um Bean para toda a aplicação (instância compartilhada), o objeto é criado assim que o Container for carregado.
- **`prototype`** → Fornece uma nova instância a cada chamada de um Bean, o objeto é criado apenas quando você **recuperar o bean** (`context.getBean()`).
- Outros: `request`, `session`, `application` (para apps web).



### 🔹Injeção via setter (`<property>`):

```xml
<bean id="customer01" class="com.exemplo.Customer">
    <!-- Injeção de dependência via setter -->
    <property name="name" value="Alessandro"/>
    <property name="age" value="22"/>
    <property name="address" ref="address01"/>
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>
```

👉 Aqui:

- **`<property>`** → usado dentro de `<bean>`
- atributos:
    - `name` → nome da propriedade (atributo) na classe
    - `value` → valor literal a ser injetado (usado para tipos primitivos).
    - `ref` → referência a outro bean (usado para Objetos)


 
### 🔹Injeção via Construtor (**`<constructor-arg>`**):

 Na I**njeção via Construtor**, é possível passar apenas os valores sem definir explicitamente qual argumento vai para qual parâmetro do Construtor.

```xml
<bean id="customer01" class="com.exemplo.Customer">
    <constructor-arg value="Alessandro"/>  <!-- name-->
    <constructor-arg value="22"/>          <!-- age-->
    <constructor-arg ref="address01"/>     <!-- address-->
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>
```
> [!WARNING]
> Fazendo dessa forma o Spring **assume que o primeiro valor vai para o primeiro parâmetro do construtor e o segundo para o segundo**. Porém se a ordem estiver errada, será lançada uma Exception.


Para evitar problemas de **ordem e ambiguidades** podemos utilizar os atributos `index` e `type`.

`index` (mais utilizado):

→ indica a **posição do parâmetro** no construtor (baseado em zero). Útil quando você tem vários parâmetros do mesmo tipo ou quer garantir a ordem sem depender da posição no XML.

```xml
<bean id="customer01" class="com.exemplo.Customer">
    <constructor-arg index="0" value="Alessandro"/>  <!-- name-->
    <constructor-arg index="1" value="22"/>          <!-- age-->
    <constructor-arg index="2" ref="address01"/>     <!-- address-->
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>
```

👉O `0` vai para o primeiro parâmetro do construtor e o `1` para o segundo, independentemente da ordem em que aparecem no XML.

`type`:

→ Indica o **tipo do parâmetro do construtor**. Usado quando há sobrecarga de construtores (ex: `Cliente(String nome)` e `Cliente(Integer idade)`). O Spring usa esse `type` para saber a qual construtor/argumento o valor pertence.

```xml
<bean id="customer01" class="com.exemplo.Customer">
    <constructor-arg type="String" value="Alessandro"/>                             <!-- name-->
    <constructor-arg type="int" value="22"/>                                        <!-- age-->
    <constructor-arg type="com.alessandromelo.entity.Address" ref="address01"/>     <!-- address-->
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>
```


### 🔹Atributo `autowire`:

Na declaração de um bean (**`<bean>`**) quando existe a dependência a outro bean (`ref`), podemos definir essa dependência sem usar **`<property>` ou `<constructor-arg>` explicitamente,** usando o atributo **`autowire`**.

Valores que o atributo `autowire` pode receber:

- `no` (default) → Não faz *autowire* automático. Você precisa declarar manualmente todas as dependências.
- `constructor` → O Spring injeta dependências pelo construtor, procurando beans compatíveis com os parâmetros.
- `byName`
- `byType`



🔸**Usando `autowire = “byName”`:**

O `autowire = “byName”` procura um bean com `id` igual ao **nome da propriedade da classe**.

```java
public class Customer{

    private Address address;

    public Customer(){
				
    }
    public Customer(Address address){
        this.address = address;
    }
		
		
    public Address getAddress() {
        return address;
    }
    public void setAddress(Address address) {
        this.address= address;
    }
    //Métodos...
}
```

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer" autowire="byName">
</bean>
          
<bean id="address" class="com.alessandromelo.entity.Address">
</bean>
```


🔸**Usando `autowire = “byType”`:**

**O `autowire = “byType”`** procura um bean do **mesmo tipo da propriedade** (mas precisa haver exatamente **um** candidato).


```java
public class Customer{
		
    private Address address;

    public Customer(){
				
    }
    public Customer(Address address){
        this.address = address;
    }
		
		
    public Address getAddress() {
        return address;
    }
    public void setAddress(Address address) {
        this.address= address;
    }
    //Métodos...
}
```

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer" autowire="byType">
</bean>
                                         
<bean class="com.alessandromelo.entity.Address">
</bean>
```
> [!WARNING]
> Se houver mais de um bean do mesmo tipo, ocorre erro (*ambiguity*).



### 🔹Atributo `primary`:

O atributo **`primary`** no Spring serve para **resolver ambiguidades** quando o container encontra **mais de um bean candidato** para injetar em uma dependência.

Por exemplo, digamos que dentro do arquivo XML declaramos dois beans do mesmo tipo, além disso estamos utilizando o `autowire = “byType”`:

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer" autowire="byType">
</bean>

<bean class="com.alessandromelo.entity.Address">
</bean>

<bean class="com.alessandromelo.entity.Address">
</bean>
```

🚨 Ao tentar executar o Spring vai lançar uma Exception de ambiguidade, dizendo que era esperado um único bean correspondente, porém foram encontrados 2 bean:

```diff
- Error creating bean with name 'alien01' defined in class path resource [spring.xml]: Unsatisfied dependency expressed through bean property 'computer': No qualifying bean of type 'com.alessandromelo.interfaces.Computer' available: expected single matching bean but found 2: com.alessandromelo.entity.Notebook#0,com.alessandromelo.entity.Notebook#1
```

Para resolver esse problema podemos usar o atributo `primary= “true”` no bean que deve ser usado:

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer" autowire="byType">
</bean>

<bean class="com.alessandromelo.entity.Address" primary="true">
</bean>

<bean class="com.alessandromelo.entity.Address">
</bean>
```

> [!WARNING]
> O atributo `primary` tem efeito quando **APENAS** **existe mais de um Bean candidato para ser injetado**, caso você defina explicitamente (usando `<property>` por exemplo)
qual o bean que deverá ser injetado e utilizar o `primary` em outro bean, o Spring irá **ignorar o bean com `primary`**.



### 🔹Lazy Init Bean:

Por padrão, os beans possuem o escopo como Singleton, ou seja, ao iniciar a aplicação, **o Spring irá gerar objetos de todos os beans que são Singleton, mesmo que você não esteja usando-os**, consequentemente gerando em muito dos casos uma **perca de performance da aplicação**.

Usando o atributo `lazy-init=”true”` podemos resolver esse problema. Com esse atributo na definição de um bean, o mesmo será criado **APENAS quando for chamado pela primeira vez**.

Imagine o mesmo exemplo anterior, temos 2 beans candidatos a injeção, sendo que apenas um deles está sendo utilizado, porém, por terem o escopo do tipo `Singleton`, 
o bean que não está sendo utilizado também será criado toda vez que o Container for iniciado. Ou seja, o Container criará beans que não estão sendo usados:

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer">
			<property name="address" ref="address01"/>
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>

<bean id="address02" class="com.alessandromelo.entity.Address"> <!-- bean que não está sendo usado, porém também será criado pelo Container-->
</bean>
```

Usando o atributo `lazy-init=”true”` na declaração do bean que NÃO está sendo usado, o mesmo será criado apenas quando for chamado:

```xml
<bean id="customer01" class="com.alessandromelo.entity.Customer">
			<property name="address" ref="address01"/>
</bean>

<bean id="address01" class="com.alessandromelo.entity.Address">
</bean>

<bean id="address02" class="com.alessandromelo.entity.Address" lazy-init="true"> <!-- bean não utilizado, agora sendo criado apenas quando for chamado-->
</bean>
```

```java
public class Main {

    public static void main(String[] args) {

        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

        Customer customer = (Customer) applicationContext.getBean("customer01");

        customer.toString();

        Address address02 = (Address) applicationContext.getBean("address02"); //Bean criado após a sua chamada
    }
}
```
> [!WARNING]
> Vale lembrar que o bean ainda continua sendo `Singleton`, o `lazy-init=”true”` altera apenas **QUANDO** o bean será criado.



