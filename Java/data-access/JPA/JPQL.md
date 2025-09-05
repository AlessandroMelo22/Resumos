# JPQL (Java Persistence Query Language)

**Linguagem de consulta orientada a objetos usada para interagir com bancos de dados relacionais dentro de aplicações Java que utilizam a Java Persistence API (JPA)** .

🟢**Vantagens em em comparação ao HQL:**

- Oferece portabilidade a outros frameworks ORM
- Mais utilizada no mercado por conta da portabilidade

🔴**Desvantagens:**

- Oferece menos recursos do que o HQL

### Características:

**Orientada a objetos:**

- Permite consultar dados usando nomes de classes (entidades) e propriedades de objetos, em vez de nomes de tabelas e colunas

**Baseada no SQL:**

- Embora orientada a objetos, a JPQL mantém uma sintaxe e estrutura semelhantes ao SQL, facilitando a transição de desenvolvedores familiarizados com SQL

**Parte da JPA:**

- A JPQL é definida e faz parte da especificação JPA, um padrão Java para mapeamento objeto-relacional (ORM).

## Primeiro passo para criação de querys JPQL:

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

Suponhamos que temos a seguinte Entidade mapeada `Student`:

```java
@Entity
public class Student{ 

    @Id
    private Long id;
    private String name;
    private Integer age;
    
    public Student(){
    }

    //Getters e Setters
}
```

### 🔹CREATE

**O JPQL NÃO suporta `INSERT ... VALUES`** (diferente de HQL), então a inserção de dados **acontece de forma “direta”, através do método `persist()`**.

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

### 🔹READ

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

### 🔹UPDATE

O JPQL **permite atualizar vários registros de uma vez (sem precisar carregar entidades na memória)**. Funciona da mesma forma que o READ, mudando apenas a sintaxe da *query*:

```java
TypedQuery<Student> query = entityManager.createQuery("UPDATE Student s SET s.name= :name WHERE s.id = :id");
query.setParameter("name", "novoNome");
query.setParameter("id", 1L);

int registrosAfetados = query.executeUpdate();
```

📌Neste código:

- `executeUpdate()` → executa uma query de modificação direta no banco, **retorna a quantidade de registros (linhas) que foram afetadas**, mas não atualiza automaticamente os objetos já carregados no `EntityManager` (Entidades buscadas pelo método `find()`).

### 🔹DELETE:

Assim como no Update, podemos excluir em massa:

```java
TypedQuery<Student> query = entityManager.createQuery("DELETE FROM Student s WHERE s.id = :id");
query.setParameter("id", 2L);

int registrosExcluidos = query.executeUpdate();
```
