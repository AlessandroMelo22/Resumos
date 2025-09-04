# Hibernate

Basicamente o Hibernate **é um Framework [ORM](), uma implementação do [JPA](colocarOLinkDoPostSobreJPA)**.


  
### Resumo:

> ORM (Mapeamento Objeto-Relacional)→ Paradigma de mapeamento
> 

> JPA → especificação Java que define um conjunto de interfaces e padrões para a persistência de dados em aplicações Java (padrão a ser seguido pelos Frameworks ORM)
> 

> Framework ORM → implementa as especificações JPA


 

# Persistência de dados usando o Hibernate:

## 1° Passo: Criando Entidades anotadas

Antes de começarmos a persistir os dados, devemos criar as nossas **Entidades** e **anota-las**.

🔹O que é uma **Entidade no contexto JPA/Hibernate**?

- É uma **classe Java** que representa uma **tabela no banco de dados**
- Cada **atributo da classe** corresponde a uma **coluna da tabela**
- O Hibernate usa **anotações** para saber como mapear **Classe Java ↔ Tabela**.

🔹As principais anotações OBRIGATÓRIAS que toda Entidade JPA deve ter são:

`@Entity` → Indica que **a classe é uma entidade JPA, mapeando-a para uma tabela no banco de dados**. 

`@Id` → **Define o campo como chave primária da entidade**.

🔹Além disso uma Entidade JPA deve ter essas **características**:

- Não ser uma Classe `final`
- Possuir Construtor vazio
- Possuir `getters` e `setters`.

Exemplo:

```java
@Entity
public class Student {

    @Id
    private Integer id;
    private String name;
    private String age;

    public Student() {
    }

    public Student(Integer id, String name, String age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
    
    //Getters e Setters
```

Além dessas anotações, o Hibernate disponibiliza outras para melhorar o mapeamento:

| **Annotation:** | **Função:** | **Parâmetros que ela pode receber:** |
| --- | --- | --- |
| `@Table` | Permite **personalizar o mapeamento para a tabela correspondente no banco de dados**.  | name, schema, catalog, indexes, uniqueConstraints|
| `@GenerateValue` | Configura a **geração automática do valor da chave primária** pelo provedor de persistência.  | strategy, generator |
| `@Column` | Especifica o mapeamento de um campo para uma coluna na tabela, permitindo definir **nome**, **tipo**, **tamanho** e **outras propriedades da coluna**. |  |
| `@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany` | Definem diferentes tipos de relacionamento entre Entidades  |  |
| `@Transient` | Serve **para indicar que um atributo de uma entidade não deve ser persistido no banco de dados**. |  |
| `@Embeddable` | A classe anotada com `@Embeddable` é considerada **um tipo incorporável**, ou seja, seus atributos **podem ser incorporados em outra entidade**. |  |
| `@Embedded` | A anotação `@Embedded` é utilizada em um campo dentro de uma entidade para indicar que ele é um objeto ***embeddable*** (da classe `@Embeddable`) |  |

🚨 Caso não anote as Entidades com `@Entity` e `@Id` será lançada as respectivas exceções:

```java
Unable to locate persister: com.alessandromelo.entity.Student
```

```java
Entity 'com.alessandromelo.entity.Student' has no identifier (every '@Entity' class must declare or inherit at least one '@Id' or '@EmbeddedId' property)
```

## 2° Passo: Arquivo `hibernate.cfg.xml`

Depois de criarmos nossas classes anotadas, devemos criar o arquivo `hibernate.cfg.xml`.

Esse arquivo é essencial para a configuração do hibernate, ele armazena as **configurações de inicialização**, fornecendo ao framework detalhes de **conexão com a base de dados (URL, usuário, senha, dialeto SQL)**, além de **propriedades do framework**, como o **modo de criação/atualização do esquema da base de dados (usando `hbm2ddl.auto`)**, além de outras configurações. Em suma ele centraliza as configurações que fazem ponte entre a aplicação Java e a base de dados relacional

Exemplo:

```java
<hibernate-configuration xmlns="http://www.hibernate.org/xsd/orm/cfg">
    <session-factory>

        <property name="hibernate.connection.driver_class">org.postgresql.Driver</property>
        <property name="hibernate.connection.url">jdbc:postgresql: //localhost:5432/hibernate_db</property>
        <property name="hibernate.connection.username">postgres</property>
        <property name="hibernate.connection.password">123456</property>

        <property name="hibernate.hbm2ddl.auto">update</property>

        <property name="hibernate.show_sql">true</property>

        <property name="hibernate.dialect">org.hibernate.dialect.PostgreSQLDialect</property>
        
        <mapping class="com.alessandromelo.entity.Student"/> //Para o mapeamento das Entity anotadaz 

    </session-factory>
</hibernate-configuration>
```

Exemplo de configurações que podem ser declaradas no arquivo **`hibernate.cfg.xml`**:

| `<property name="hibernate.connection.driver_class">org.postgresql.Driver</property>` | Especifica a classe do driver JDBC que o Hibernate utilizará para se conectar ao banco de dados. (No exemplo o Driver do PostgreSQL) | - |
| --- | --- | --- |
| `<property name="hibernate.connection.url">jdbc:postgresql://localhost:5432/teste_db</property>` | URL do Banco de dados | - |
| `<property name="hibernate.connection.username">postgres</property>` | Usuário do Banco de dados | - |
| `<property name="hibernate.connection.password">123456</property>` | Senha do Banco de dados | - |
| `<property name="hibernate.hbm2ddl.auto">update</property>` | O Hibernate irá detectar as alterações feitas nas classes de entidade mapeadas e atualizará o esquema do banco de dados para corresponder a essas mudanças, sem destruir os dados existentes. | - |
| `<property name="hibernate.hbm2ddl.auto">create</property>` | O Hibernate descarta o esquema existente (se houver) e cria um novo esquema com base nas suas entidades e mapeamentos. | - |
| `<property name="hibernate.hbm2ddl.auto">validate</property>` | O Hibernate **valida se o esquema existente corresponde às suas entidades e mapeamentos.** Se houver discrepâncias, uma exceção é lançada, o que é útil para garantir que o esquema esteja correto antes da execução da aplicação. | - |
| `<property name="hibernate.show_sql">true</property>` | **Mostra no console a query SQL que está sendo realizada** | false (default), true |
| `<property name="hibernate.format_sql">true</property>` | **Mostra no console a query SQL que está sendo realizada, porém, de forma formatada** | false (default), true |
| `<property name="hibernate.dialect">org.hibernate.dialect.PostgreSQLDialect</property>` | Estabelece **qual será o dialeto utilizado** | - |
| `<mapping class="com.example.MinhaClasse"/>` | Serve justamente para **registrar uma classe anotada (@Entity) para que o Hibernate saiba que ela deve ser considerada na geração do esquema e no gerenciamento de persistência**. |  |

🚨 Sem a criação do arquivo `hibernate.cfg.xml` será lançada a seguinte exceção:

```java
Could not locate cfg.xml resource [hibernate.cfg.xml]
```

## 3° Passo: Carregando as Configurações e criando uma `SessionFactory`

Como próximo passo devemos criar uma instância da classe `Configuration` fornecida pelo próprio Hibernate.

- A `Configuration` **é um objeto de inicialização e definição de metadados**. Ela só é usada no arranque da aplicação (fase de bootstrap).

```java
Configuration configuration = new Configuration();
```

A partir dessa instância, podemos por exemplo usar o método `configure()` que serve basicamente para **carregar as configurações feitas no arquivo `hibernate.cfg.xml`**:

```java
configuration.configure();
```

Além disso é com a instância da classe `Configuration` que podemos criar uma `SessionFactory`, sendo essa a Interface **responsável por abrir as Sessões.**

- A `SessionFactory` representa a **fábrica de sessões**
- É criada **uma vez** durante a inicialização da aplicação.
- É **thread-safe**: pode ser compartilhada entre várias threads.
- Custo de criação **alto**, por isso geralmente só existe **uma instância** por aplicação (ou por banco de dados).

```java
SessionFactory sessionFactory = configuration.buildSessionFactory(); //Para criar uma SessionFactory
```

## 4° Passo: Criando `Session` e `Transaction`

A partir da criação da `SessionFactory` podemos instanciar objetos do tipo `Session`. 

- Um objeto do tipo `Session` **representa uma conexão com o banco de dados (uma unidade de trabalho)**.
- A `Session` herda da Interface `EntityManager` do JPA
- Através dele que você **executa operações** sobre as entidades
- É **leve** e **não thread-safe** (cada thread deve ter sua própria `Session`).

```java
Session session = sessionFactory.openSession(); //Para abrir uma Sessão
```

Com um objeto do tipo `Session` criado, podemos criar objetos do tipo `Transaction`. 

- Um objeto do tipo `Transaction` representa uma **transação no banco de dados**.
- É recomendado que **operações onde há manipulação de dados (INSERT, UPDATE, DELETE…), sejam executadas dentro de uma `Transaction`**.
- Garante as propriedades **ACID** (**Atomicidade**, **Consistência**, **Isolamento**, **Durabilidade**).
- Sempre associada a uma `Session`.

```java
Transaction transaction = session.beginTransaction(); //Para iniciar a transação

transaction.commit(); //Para confirmar a transação
transaction.rollback() //Para desfazer a transação
```

🚨 Observação:

> ⚠️NÃO é obrigatório a criação de uma `Transaction`, mas é recomendado

</aside>

## 5° Passo: Operações CRUD

Após realizarmos as devidas configurações de inicialização, podemos começar a **persistência de dados em cima das Entidades criadas**.

### CREATE:

Para **salvar dados** no banco utilizamos o método `persist(Object)`. Como se trata de uma operação de Manipulação de dados, devemos realizar a operação dentro de uma Transação (`Transaction`)

```java
Student student01 = new Student(1, "Alessandro", "22"); //Objeto a ser persistido

SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory(); //Com uma Configuration criamos um SessionFactory

Session session = sessionFactory.openSession(); 

Transaction transaction = session.beginTransaction();

session.persist(student01); 

transaction.commit();
```

### READ:

Para realizar a busca de dados, usamos o método `get(Class<T> var1, Object var2)` (ou o método `find(Class<T> var1, Object var2)` fornecido pelo `EntityManager`), vale lembrar que operações de **busca NÃO manipula os dados**, então NÃO é preciso de uma `Transaction`  

Exemplo:

```java
SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory(); 

Session session = sessionFactory.openSession(); 

Student student01 = session.get(Student.class, 1); //Aqui o método recebe a classe da Entidade e o Id (PK) da Entidade a ser buscada

System.*out*.println(student02); 
```



### UPDATE:

Para realizar **alterações** em um dado no banco, usamos o método `merge(Object o)`, caso o **Id** do Objeto passado exista no banco, será realizado a **alteração**, caso contrario será **criado um novo dado no banco**. Vale lembrar que se trata de uma Manipulação de dados, então devemos ter uma `Transaction`

Exemplo:

```java
Student student01 = new Student(1, "Maria Eduarda", "21"); //Como o Objeto com o Id 01 já existe, os campos serão alterados

SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory(); 

Session session = sessionFactory.openSession(); 

Transaction transaction = session.beginTransaction();

session.merge(student01);

transaction.commit();

```

### DELETE:

Para realizarmos a **exclusão** de dados, utilizamos o método `remove(Object o)`, e como precisamos passar o Objeto a ser deletado podemos primeiro realizar uma busca com o `get()` e depois passar o Objeto para o método `remove(Object o)`. Por ser uma Manipulação de dados, devemos ter uma `Transaction`.

Exemplo:

```java
SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory(); 
        
Session session = sessionFactory.openSession(); 

Student student01 = session.get(Student.class, 1); //Realiza o SELECT 

Transaction transaction = session.beginTransaction();

session.remove(student01); //Passamos o Objeto que foi retornado no SELECT

transaction.commit();
```

## 6° Passo: Fechamento dos recursos

Depois de realizar qualquer tipo de operação, devemos lembrar de fechar a conexão dos recursos (manualmente ou utilizando um `try-with-resources`), sendo sempre na ordem inversa de criação (`Transaction` →`Session` → `SessionFactory`…)

`Transaction` → Após realizar qualquer operação (`commit()` caso a operação seja um sucesso e `rollback()` caso ocorra algum erro)

`Session` → Após finalizar a Operação, mesmo tendo sucesso ou não (`close()`)

`SessionFactory` → Quando a aplicação encerra (`close()`)

Exemplo:

```java
Student student01 = new Student(1, "Alessandro", "22"); //Objeto a ser persistido

SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory(); //Com uma Configuration criamos um SessionFactory

Session session = sessionFactory.openSession(); 

Transaction transaction = session.beginTransaction();

session.persist(student01); 

transaction.commit();

//FECHAMENTO DOS RECURSOS:
session.close();
sessionFactory.close();

```

Recursos que todas as aplicações Standalone devem ter:

| 
Recurso:
 | 
O que ele faz: |  |
| --- | --- | --- |
| Arquivo de configuração (`hibernate.cfg.xml`) | - Define dados de conexão(JDBC URL, usuário, senha)
- Dialeto SQL (`hibernate.dialect`)
- Estratégia de criação/validação do schema (`hibernate.hbm2ddl.auto`)
- Lista de classes anotadas (`<mapping class="..."/>`).
 |  |
| Classe `Configuration` | - Possui o método `configure()` que serve para carregar as configurações do arquivo `hibernate.cfg.xml`
- Usada para criar `SessionFactory`
 |  |
| `SessionFactory` | - É o **coração do Hibernate**: carrega *metadados*, gerencia pool de conexões.
- Cria e fornece `Session` para realizar operações com o banco.
 |  |
| `Session` | - É a **conexão lógica** com o banco. Você precisa dela para **fazer CRUD e queries**.
- Sem `Session`, não há persistência nem leitura.
 |  |
| `Transaction` | - Necessário quando há manipulação de dados (**INSERT**, **UPDATE**, **DELETE**…)
- O Hibernate só envia as alterações ao banco quando você está dentro de uma transação.
- Garante consistência. Se algo der errado, você pode fazer `rollback()`.
 |  |
| Código de gerenciamento do ciclo de vida | É necessário cuidar manualmente de:
- **Abrir e fechar sessões**.
- **Iniciar e finalizar transações**.
- **Fechar a** `SessionFactory` **no shutdown da aplicação**. |  |

## Relacionamento entre Entidades:

Digamos que temos duas Entidades: **`Estudante`** e **`Notebook`**, e queremos mapear e representar o relacionamento entre essas entidades.

Para esse contexto, podemos ter vários tipos de Relacionamentos entre elas, por exemplo:

(**1:1**) → Um Estudante pode ter no **máximo 1** Notebook e um Notebook pertence a **apenas 1** Estudante;

(**1:N**) → Um Estudante pode ter **N** Notebooks e um Notebook pertence a **apenas 1** Estudante;

(**N:N**) → Um Estudante pode ter **N** Notebooks e um Notebook pode pertencer a **N** Estudantes;

### Direcionalidade do Relacionamento no JPA

Além dos tipos de Relacionamentos, também definimos a **direção do Mapeamento** na **camada de domínio (aplicação)**:

- **Unidirecional:** apenas uma entidade conhece a outra.
    - Ex: **`Estudante`** armazena uma referência de **`Notebook`**, porém **`Notebook`**não armazena a referencia a **`Estudante`**

- **Bidirecional:** ambas as entidades conhecem e mantêm referência uma à outra.
    - Ex: **`Estudante`** possui referência de **`Notebook`**, e **`Notebook`** possui referência de **`Estudante`**.

**🚨IMPORTANTE:**

<aside>
⚠️

O conceito de bidirecionalidade **NÃO existe no contexto de Banco de Dados,** a direção do relacionamento é sempre de **quem tem a foreign key (FK) para quem ela aponta** (quem possui a FK conhece o outro lado da relação).

A **Direcionalidade** é possível apenas no escopo da **camada de domínio (aplicação)**, sendo utilizada principalmente para **navegação e consultas entre Entidades**, permitindo acessar informações da primeira Entidade a partir da segunda e vice-versa.

</aside>

### Bidirecionalidade e lado proprietário da relação:

Sempre que criamos um **relacionamento Bidirecional**, devemos definir qual **o lado proprietário da relação** (lado responsável por realizar o mapeamento), caso contrário será gerado **redundâncias no mapeamento** (mais de uma referencia de relacionamento). Para isso, usamos o atributo `mappedBy`.

### `mappedBy`:

O atributo `mappedBy` indica que o outro lado da relação é o proprietário. Funciona da seguinte forma, dentro do lado **NÃO PROPRIETÁRIO** usamos o `mappedBy` para **referenciar o lado PROPRIETÁRIO**.

Por exemplo:

No relacionamento entre Estudante e Notebook, o **lado proprietário** é o **Notebook** (é quem armazena a FK e fica responsável pelo mapeamento), então dentro de Estudante será usado o `mappedBy` para referenciar o lado dominante (Notebook).

PS: colocar exemplo de relacionamento unidirecional e bidirecional

 

Para realizar o Mapeamento de cada cenário possível, utilizamos annotations específicas fornecidas pelo JPA:

## Relacionamento 1:1:

---

Para esse relacionamento temos a annotation `@OneToOne`. Então basta que o **atributo que representa o relacionamento seja anotado com** `@OneToOne`.

Unidirecional:

```java
@Entity
public class Estudante{

    @Id
    private Long id;
    private String name;
    
    @OneToOne
    private Notebook notebook; 
```

Bidirecional:

```java

@Entity
public class Estudante{ //lado proprietário

    @Id
    private Long id;
    private String name;
    
    @OneToOne
    private Notebook notebook;

@Entity
public class Notebook {

    @Id
    private Long id;
    private String brand;
    
    @OneToOne(mappedBy = "notebook")
    private Estudante estudante;
```

As boas práticas de **definição de FK** dizem que em um relacionamento **1:1**:

| 
Situação:
 | 
Exemplo: | 
Lado que a **FK** deve ficar: |
| --- | --- | --- |
| Se não houver obrigatoriedade em nenhum dos lados **OU** 
Se houver nos dois lados | **(0:1**) —🔸— (**0:1)
          OU
(1:1**) —🔸— (1**:1)** | Em qualquer um dos lados (de preferência no que faça sentido contextualmente) |
| Se houver obrigatoriedade em um dos lados | (**1:1**) —🔸— (**0:1**) | No lado que é obrigado a se relacionar |

## Relacionamento **N:N**:

---

Para esse relacionamento temos a annotation `@ManyToMany`. Então basta que o atributo que representa o relacionamento seja anotado com `@ManyToMany`.

Unidirecional:

```java
@Entity
public class Estudante{

    @Id
    private Long id;
    private String name;
   
    @ManyToMany
    private List<Notebook> notebooks;
```

Bidirecional:

```java

@Entity
public class Estudante{ //lado proprietário

    @Id
    private Long id;
    private String name;
    
    @ManyToMany
    private List<Notebook> notebooks;

@Entity
public class Notebook {

    @Id
    private Long id;
    private String brand;
    
    @ManyToMany(mappedBy = "notebooks")
    private List<Estudante> estudantes;
```

Boas práticas para a definição da FK em relacionamentos **N:N**:

| 
**Situação:**
 | 
**Exemplo:** | 
**Lado que a FK deve ficar:** |
| --- | --- | --- |
| Independente se houver obrigatoriedade ou não | **(0:N**) —🔸— (**0:N)
          OU
(1:N**) —🔸— (1**:N)
          OU
(0:N**) —🔸— (1**:N)** | Pode ser adicionado em **qualquer lado (de preferencia no que faça sentido contextual)**

O Hibernate irá criar uma **tabela associativa** que servira apenas **para armazenar as chaves (FK) de ambas as Entidades** |

## Relacionamento 1:N:

---

Para esse relacionamento temos as annotations `@OneToMany` e `@ManyToOne`. No caso do relacionamento **1:N**, temos que nos atentar a **perspectiva** de cada lado do relacionamento para **usarmos as annotations de forma certa**.

Por exemplo, digamos que **um Estudante possa ter N Notebooks e um Notebook pertença a 1 Estudante**. Então analisando a **perspectiva** de cada lado, o resultado deveria ficar assim:

Ponto de vista de Estudante:

<aside>
🧠

1 Estudante para N Notebooks → **`@OneToMany`**

</aside>

```java
@Entity
public class Estudante{ 

    @Id
    private Long id;
    private String name;
    
    @OneToMany(mappedBy = "estudante")
    private List<Notebook> notebooks;
```

Ponto de vista de Notebook (inverso de Estudante):

<aside>
🧠

N Notebooks para 1 Estudante → **`@ManyToOne`**

</aside>

```java
@Entity
public class Notebook { //lado proprietário

    @Id
    private Long id;
    private String brand;
    
    @ManyToOne
    private Estudante estudante;
```

Boas práticas para a definição da FK em relacionamentos **1:N**:

| 
Situação:
 | 
Exemplo: | 
Lado que a **FK** deve ficar: |
| --- | --- | --- |
| Independente se houver obrigatoriedade ou não | **(0:1**) —🔸— (**0:N)
          OU
(1:1**) —🔸— (1**:N)
          OU
(0:1**) —🔸— (1**:N)** | No lado **N** da relação |

| mappedBy | indica quem está realizando o **mapeamento**. Qual o **lado proprietário da Chave Estrangeira (lado dominante)** | `@OneToMany`
`@ManyToMany` |
| --- | --- | --- |
|  |  |  |
|  |  |  |

---

## Busca Ansiosa e Preguiçosa (Eager e Lazy Fetch):

Quando temos relacionamentos no JPA/Hibernate (`@OneToMany`, `@ManyToOne`, `@OneToOne`, `@ManyToMany`), existem duas formas de principais de carregar os dados:

### Lazy (`FetchType.*LAZY*`)

Carrega apenas os dados da entidade principal, os **dados relacionados** (dados da entidade que se relaciona com a entidade principal) só são carregados **quando você realmente acessa o relacionamento** no código.

**Como funciona:**

- Quando você carrega a entidade principal, o relacionamento vem como um **proxy** (um objeto "preguiçoso").
- O Hibernate só dispara a query para buscar os **dados relacionados** no momento em que você **solicitar explicitamente o carregamento destes (**utilizando o método `getter` por exemplo**)**.

**✅Vantagens:**

- Mais **eficiente**: não busca dados que talvez você nem use.
- Bom para performance em listas grandes.

**❌Desvantagens:**

- Pode causar **`LazyInitializationException`** se você acessar o relacionamento **fora da sessão** (por exemplo, depois de fechar a transação).

### Eager (`FetchType.*EAGER*`)

Os dados relacionados **são carregados imediatamente junto com a entidade principal**, independentemente de você usar ou não.

**Como funciona:**

- Ao buscar a entidade principal, o Hibernate faz **JOIN** (ou múltiplas queries) para trazer todos os dados relacionados na mesma hora.

**✅Vantagens:**

- Evita `LazyInitializationException`, pois já traz tudo.
- Útil quando você **sempre** precisa dos dados relacionados.

**❌Desvantagens:**

- Pode trazer **dados desnecessários**.
- Pode causar **queries gigantes** ou **N+1 problem**.
- Prejudica performance se não for usado com cuidado.

🚨IMPORTANTE:

<aside>
⚠️

Cada annotation possui um tipo de carregamento **padrão** caso não seja especificado

</aside>

| **Annotation** | **Padrão JPA** | **Padrão Hibernate** |
| --- | --- | --- |
| `@ManyToOne` | EAGER | EAGER |
| `@OneToOne` | EAGER | EAGER |
| `@OneToMany` | LAZY | LAZY |
| `@ManyToMany` | LAZY | LAZY |

---

## Hibernate Caching:

O Hibernate trabalha com o conceito de **Cache**, diferente do JDBC puro.

### O que seria Cache?

Cache **é uma técnica que permite que dados acessados com frequência sejam armazenados na memória para que possam ser recuperados rapidamente sem a necessidade de retornar ao banco de dados todas as vezes**.  Ao armazenar dados em cache, o Hibernate pode reduzir o número de consultas ao banco de dados que precisa executar, melhorando o desempenho e reduzindo a carga no servidor de banco de dados.

### Níveis de Cache do Hibernate

O Hibernate oferece cache em três níveis:

### L1 (ou cache de sessão):

**Como utilizar?**

- Cache padrão do Hibernate

**Escopo:**

- `Session`
    - Está ligado à `Session` do Hibernate (ou `EntityManager` no JPA), ou seja, funciona durante todo o ciclo de vida do objeto `Session`, além disso ele não pode ser acessado de outras `Session` criadas pelo `SessionFactory`.

**Funcionamento:**

- Quando uma entidade é consultada, o Hibernate verifica se ela já está no cache da sessão atual. Se estiver, a entidade é retornada do cache, evitando acesso ao banco de dados. Caso contrário, a entidade é carregada do banco, adicionada ao cache da sessão e então retornada.

### L2 (ou Cache de Segundo Nível):

**Como utilizar?**

- É necessário habilitar o cache de segundo nível no arquivo de configuração do Hibernate (`hibernate.cfg.xml`) e configurar um provedor de cache, como o Ehcache, e anotar as entidades que devem ser armazenadas em cache com a anotação `@Cacheable`.

**Escopo:** 

- `SessionFactory`
    - Está ligado à `SessionFactory`, ou seja, está disponível para todos os objetos `Session` criados pelo `SessionFactory`.

**Funcionamento:**

- Similar ao cache de primeiro nível, mas com escopo maior. Se uma entidade não for encontrada no cache de primeiro nível, o Hibernate verifica se ela está no cache de segundo nível. Se estiver, a entidade é carregada do cache e adicionada ao cache de primeiro nível da sessão atual, antes de ser retornada.

### Query Cache:

**Escopo:** 

- `SessionFactory`
    - Similar ao cache de segundo nível.

**Como utilizar?**

São os mesmos passos para o cache L2.

**Funcionamento:**

- Armazena os resultados de consultas, permitindo que consultas repetidas retornem resultados do cache, evitando consultas ao banco de dados.

---

## HQL (Hibernate Query Language:

Definição:

é uma **linguagem de consulta orientada a objetos usada pelo framework Hibernate para interagir com bancos de dados.**

Derivada do SQL, porém trabalha com os objetos Java e seus atributos ao invés de tabelas e colunas do banco. 

Permite manipular dados de forma mais intuitiva, utilizando conceitos como herança, polimorfismo e associações. 

Consultas HQL são convertidas pelo Hibernate em consultas SQL nativas, facilitando a migração entre diferentes bancos de dados. 

Características:

- Possui mais recursos que a JPQL porém a aplicação fica “presa” ao Hibernate

## JPQL (Java Persistence Query Language)

**Linguagem de consulta orientada a objetos usada para interagir com bancos de dados relacionais dentro de aplicações Java que utilizam a Java Persistence API (JPA)** .

**Vantagens em em comparação ao HQL:**

- Oferece portabilidade a outros frameworks ORM
- Mais utilizada no mercado por conta da portabilidade

**Desvantagens:**

- Oferece menos recursos do que o HQL

### Características:

**Orientada a objetos:**

Permite consultar dados usando nomes de classes (entidades) e propriedades de objetos, em vez de nomes de tabelas e colunas

**Baseada no SQL:**

Embora orientada a objetos, a JPQL mantém uma sintaxe e estrutura semelhantes ao SQL, facilitando a transição de desenvolvedores familiarizados com SQL

**Parte da JPA:**

A JPQL é definida e faz parte da especificação JPA, um padrão Java para mapeamento objeto-relacional (ORM).

### Primeiro passo para criação de querys JPQL:

Depois de criarmos as nossas Entidades mapeadas (Classe Java com `@Entity` e `@Id`), o primeiro passo é criar uma `EntityManagerFactory`, responsável por fornecer instâncias de `EntityManager`. **É através da `EntityManager` que podemos criar e executar nossas consultas (queries)**.

`EntityManager` x `Session`

- Quando usamos o JPA diretamente, trabalhamos sempre com a `EntityManager`
- Usando o Hibernate, trabalhamos com a `Session` que faz o mesmo papel
- Por baixo dos panos, o Hibernate implementa a JPA fazendo com que a `Session` seja uma extensão (ou adaptação) da `EntityManager`

```java
EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("");
EntityManager entityManager = entityManagerFactory.createEntityManager();
```

ou assim:

```java
@PersistenceContext
EntityManager entityManager;
```

## CRUD com JPQL:

### CREATE

**O JPQL NÃO suporta INSERT …. VALUES** (diferente de HQL), então a inserção de dados **acontece de forma “direta”, através do método `persist()`**.

Vale lembrar que por ser uma Manipulação de dados, é recomendado criar uma `Transaction`:

```java
EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("");
EntityManager entityManager = entityManagerFactory.createEntityManager();

Student student = new Student(1L, "Alessandro", 22);

EntityTransaction transaction = entityManager.getTransaction(); //Cria uma transaction
transaction.begin(); //Inicia a Transação

entityManager.persist(student); //Realiza a inserção do dado

transaction.commit() //Confirma a transação
```

### READ

**Buscar todos:**

Para buscar todos os registros de uma Entidade, usamos a seguinte sintaxe:

```java
TypedQuery<Student> query = entityManager.createQuery("SELECT s FROM Student s", Student.class);

List<Student> students = query.getResultList();
```

📌Neste código:

- `createQuery(String query, Myclass.class)` → método usado para criar as querys JPQL, retorna um objeto do tipo `TypedQuery<Myclass>`, já com o tipo esperado (`Student` seguindo o exemplo), sem necessidade de *cast* manual.
- `Student` → **nome da entidade**, não da tabela.
- `s` → um **alias** (**apelido temporário** atribuído a colunas ou tabelas em uma consulta), como no SQL.
- `getResultList()` → método usado para executar a query, quando se **espera uma lista de resultados**, retorna um `List<T>` do tipo que foi declarado na criação da query (no caso `Student`).

**Buscar por parâmetros:**

Para realizar a busca de algum registro por parâmetro, usamos a seguinte sintaxe:

Parâmetro nomeado 

```java
TypedQuery<Student> query = entityManager.createQuery("SELECT s FROM Student s WHERE s.id = :id");

query.setParameter("id",3L); //recebe o nome do parâmetro e o valor que ele terá

Student student = query.getSigleResult();
```

Parâmetro posicional

```java
TypedQuery<Student> query = entityManager.createQuery("SELECT s FROM Student s WHERE s.name = ?1");

query.setParameter(1,"Maria"); //recebe o número do parâmetro e o valor que ele terá

List<Student> students = query.getResultList();
```

📌Nestes códigos:

- `s.id` e `s.name` → **atributos da entidade**, não colunas do banco.
- `:id` → prefixo usado para criar um parâmetro nomeado (`:nomeDoParâmetro`)
- `?1` → prefixo usado para criar um parâmetro posicional (`?n`, `n` sendo um número ordinal que começa em 1)
- `setParameter()` → **método usado para *settar* valores nos parâmetros**.
- `getSingleResult()` → usado para executar a query, **quando se espera apenas um resultado**, **retorna 1 objeto do tipo declarado na query** (ou erro se não encontrar).

### UPDATE

O JPQL **permite atualizar vários registros de uma vez (sem precisar carregar entidades na memória)**. Funciona da mesma forma que o READ, mudando apenas a sintaxe da *query*:

```java
TypedQuery<Student> query = entityManager.createQuery("UPDATE Student s SET s.name= :name WHERE s.id = :id");
query.setParameter("name", "novoNome");
query.setParameter("id", 1L);

int registrosAfetados = query.executeUpdate();
```

📌Neste código:

- `executeUpdate()` → executa uma query de modificação direta no banco, **retorna a quantidade de registros (linhas) que foram afetadas**, mas não atualiza automaticamente os objetos já carregados no `EntityManager` (Entidades buscadas pelo método `find()`).

### DELETE:

Assim como no Update, podemos excluir em massa:

```java
TypedQuery<Student> query = entityManager.createQuery("DELETE FROM Student s WHERE s.id = :id");
query.setParameter("id", 2L);

int registrosExcluidos = query.executeUpdate();
```

**DICIONÁRIO:**

***Mapeamento**: “É o **processo de associar** os **atributos de uma classe Java** aos **campos de uma tabela no banco de dados**.”*

***EntityManager**: Interface fornecida pela especificação JPA que fica responsável por gerenciar as entidades além de permitir executar operações CRUD e queries dentro de um contexto de persistência.*

***Transação**: é um **conjunto de operações que são tratadas como uma única unidade de trabalho.** Isso significa que todas as operações dentro da transação devem ser executadas com sucesso para que a transação seja confirmada (commit), caso contrário, todas as operações devem ser desfeitas (rollback), retornando o sistema ao seu estado anterior.

**Camada de domínio**: **é uma camada opcional que fica entre a interface do usuário e a camada de dados, e é responsável por encapsular a lógica de negócios (aplicação)***
