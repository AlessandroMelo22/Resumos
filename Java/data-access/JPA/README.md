# JPA (Java Persistence API)

## O que é JPA?

Se trata de uma **especificação Java**, ou seja, um **conjunto de interfaces e padrões** que definem como deve ser feita a persistência de dados em Java, **seguindo o paradigma de ORM**.  
Por ser apenas uma **especificação**, ele não possui implementação própria — quem implementa é um **Framework ORM** como o **Hibernate**, **EclipseLink**, **OpenJPA**, etc.

### Resumo:

> **JPA** → Define **o padrão** (interfaces, anotações, regras)
>
> **Framework ORM** → **Implementa** o padrão definido pelo JPA

---

## O que o JPA especifica?

O JPA define regras e contratos em diversas áreas da persistência de dados:

---

## 1. Entidades (`@Entity`)

O JPA especifica o conceito de **Entidade**: uma classe Java que representa uma tabela no banco de dados.

### Regras obrigatórias para uma Entidade JPA:

- Deve ser anotada com `@Entity`
- Deve possuir um campo anotado com `@Id` (chave primária)
- Não pode ser uma classe `final`
- Deve possuir um **construtor sem argumentos** (pode ser `protected` ou `public`)
- Deve possuir `getters` e `setters`

```java
@Entity
public class Produto {

    @Id
    private Long id;
    private String nome;
    private Double preco;

    public Produto() {}

    // Getters e Setters
}
```

---

## 2. Anotações de Mapeamento

O JPA define um conjunto de anotações para mapear a classe Java ao esquema relacional do banco:

| **Anotação** | **Função** |
|---|---|
| `@Entity` | Marca a classe como uma entidade JPA |
| `@Table` | Personaliza o nome e esquema da tabela correspondente |
| `@Id` | Define o campo como chave primária |
| `@GeneratedValue` | Configura a geração automática do valor da chave primária |
| `@Column` | Personaliza o mapeamento de um atributo para uma coluna (nome, tamanho, nullable, etc.) |
| `@Transient` | Indica que o atributo **não deve ser persistido** no banco |
| `@Embeddable` | Marca uma classe como incorporável em outra entidade |
| `@Embedded` | Incorpora um objeto `@Embeddable` dentro de uma entidade |
| `@Enumerated` | Define como um `enum` deve ser persistido (`ORDINAL` ou `STRING`) |
| `@Temporal` | Define o tipo de dado temporal (`DATE`, `TIME` ou `TIMESTAMP`) — mais usado antes do Java 8 |
| `@Lob` | Indica que o campo é um objeto grande (`BLOB` ou `CLOB`) |

### `@GeneratedValue` — Estratégias de geração de ID:

| **Strategy** | **Descrição** |
|---|---|
| `GenerationType.AUTO` | O provedor escolhe a estratégia mais adequada para o banco |
| `GenerationType.IDENTITY` | Usa uma coluna auto-incremental do banco (ex: `AUTO_INCREMENT` no MySQL) |
| `GenerationType.SEQUENCE` | Usa uma sequência do banco de dados (ex: PostgreSQL, Oracle) |
| `GenerationType.TABLE` | Usa uma tabela auxiliar para simular uma sequência |

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

---

## 3. Relacionamentos entre Entidades

O JPA especifica anotações para mapear os diferentes tipos de relacionamento entre entidades:

| **Anotação** | **Relação** | **Exemplo** |
|---|---|---|
| `@OneToOne` | 1 para 1 | Um usuário tem um endereço |
| `@OneToMany` | 1 para N | Um pedido tem vários itens |
| `@ManyToOne` | N para 1 | Vários itens pertencem a um pedido |
| `@ManyToMany` | N para N | Vários alunos cursam várias disciplinas |

### Atributos comuns nas anotações de relacionamento:

| **Atributo** | **Função** |
|---|---|
| `mappedBy` | Indica o lado **não proprietário** da relação (evita mapeamento duplicado) |
| `cascade` | Define quais operações se propagam para a entidade relacionada |
| `fetch` | Define a estratégia de carregamento (`LAZY` ou `EAGER`) |
| `optional` | Define se o relacionamento é obrigatório ou não |

### `CascadeType` — Tipos de Cascata:

| **CascadeType** | **Descrição** |
|---|---|
| `PERSIST` | Ao persistir a entidade principal, persiste também as relacionadas |
| `MERGE` | Ao fazer merge, aplica também nas relacionadas |
| `REMOVE` | Ao remover a entidade principal, remove também as relacionadas |
| `REFRESH` | Ao atualizar o estado da entidade principal, atualiza também as relacionadas |
| `DETACH` | Ao desanexar a entidade principal, desanexa também as relacionadas |
| `ALL` | Aplica todos os tipos acima |

```java
@OneToMany(mappedBy = "pedido", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
private List<Item> itens;
```

---

## 4. `EntityManager` — Interface Central do JPA

O `EntityManager` é a **interface central da especificação JPA**, responsável por gerenciar o ciclo de vida das entidades e executar operações de persistência.

> É o equivalente JPA da `Session` do Hibernate (que inclusive herda dela).

### Principais métodos:

| **Método** | **Operação** | **Descrição** |
|---|---|---|
| `persist(Object)` | INSERT | Persiste uma nova entidade no banco |
| `find(Class, id)` | SELECT | Busca uma entidade pelo ID |
| `merge(Object)` | UPDATE | Atualiza uma entidade existente (ou cria se não existir) |
| `remove(Object)` | DELETE | Remove uma entidade do banco |
| `flush()` | — | Sincroniza o estado do contexto de persistência com o banco |
| `refresh(Object)` | — | Recarrega o estado da entidade do banco |
| `detach(Object)` | — | Desanexa a entidade do contexto de persistência |
| `contains(Object)` | — | Verifica se a entidade está sendo gerenciada |
| `createQuery(String)` | — | Cria uma query JPQL |
| `createNamedQuery(String)` | — | Executa uma `@NamedQuery` |
| `createNativeQuery(String)` | — | Executa uma query SQL nativa |

---

## 5. `EntityManagerFactory`

Assim como o JPA especifica o `EntityManager`, também especifica a `EntityManagerFactory`, que é responsável por **criar instâncias de `EntityManager`**.

> É o equivalente JPA da `SessionFactory` do Hibernate.

- Criada uma vez durante a inicialização da aplicação
- É **thread-safe**
- Custo de criação **alto**

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("minha-unidade");
EntityManager em = emf.createEntityManager();
```

---

## 6. Contexto de Persistência

O JPA especifica o conceito de **Contexto de Persistência**: um ambiente onde as entidades são gerenciadas pelo `EntityManager`.

### Estados de uma Entidade no JPA:

| **Estado** | **Descrição** |
|---|---|
| **Transient (Transitório)** | O objeto foi criado mas ainda não está associado a nenhum contexto de persistência e não possui representação no banco |
| **Managed (Gerenciado)** | O objeto está associado ao contexto de persistência e qualquer alteração é rastreada automaticamente |
| **Detached (Desanexado)** | O objeto já foi gerenciado mas o contexto de persistência foi fechado ou ele foi explicitamente desanexado |
| **Removed (Removido)** | O objeto está marcado para ser deletado do banco na próxima sincronização |

```
new Objeto()    →   persist()   →   [Managed]
                                        ↓
                                    detach() / close()
                                        ↓
                                    [Detached]
                                        ↓
                                    merge()
                                        ↓
                                    [Managed]
                                        ↓
                                    remove()
                                        ↓
                                    [Removed]
```

---

## 7. Transações — `EntityTransaction`

O JPA especifica a interface `EntityTransaction` para controle de transações em aplicações standalone (fora de um container como o Spring ou Jakarta EE).

```java
EntityTransaction tx = em.getTransaction();
tx.begin();
// operações...
tx.commit();   // confirma
tx.rollback(); // desfaz em caso de erro
```

> Em contextos gerenciados (Spring, Jakarta EE), o controle de transação é feito pelo container via `@Transactional`, sem necessidade de gerenciamento manual.

---

## 8. JPQL (Java Persistence Query Language)

O JPA especifica a **JPQL**, uma linguagem de consulta orientada a objetos similar ao SQL, mas que opera sobre **entidades e seus atributos** ao invés de tabelas e colunas.

### Características:

- Portável entre diferentes bancos de dados
- Trabalha com os **nomes das classes e atributos Java**, não com tabelas/colunas
- Suporta `JOIN`, `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING`
- **Não suporta** recursos específicos de banco (funções proprietárias, hints, etc.)

```java
// SQL equivalente: SELECT * FROM produto WHERE preco > 100
TypedQuery<Produto> query = em.createQuery(
    "SELECT p FROM Produto p WHERE p.preco > :preco", Produto.class
);
query.setParameter("preco", 100.0);
List<Produto> produtos = query.getResultList();
```

### JPQL vs HQL:

| | **JPQL** | **HQL** |
|---|---|---|
| **Origem** | Especificação JPA | Exclusivo do Hibernate |
| **Portabilidade** | Funciona em qualquer implementação JPA | Preso ao Hibernate |
| **Recursos** | Básicos (definidos pela spec) | Mais recursos que a JPQL |

---

## 9. `persistence.xml`

O JPA especifica o arquivo `persistence.xml` como o arquivo de configuração padrão de uma **Unidade de Persistência** (`Persistence Unit`).

> É o equivalente JPA do `hibernate.cfg.xml`

```xml
<persistence xmlns="https://jakarta.ee/xml/ns/persistence" version="3.0">
    <persistence-unit name="minha-unidade">
        <class>com.exemplo.Produto</class>
        <properties>
            <property name="jakarta.persistence.jdbc.driver" value="org.postgresql.Driver"/>
            <property name="jakarta.persistence.jdbc.url" value="jdbc:postgresql://localhost:5432/meu_banco"/>
            <property name="jakarta.persistence.jdbc.user" value="postgres"/>
            <property name="jakarta.persistence.jdbc.password" value="123456"/>
        </properties>
    </persistence-unit>
</persistence>
```

> No Spring Boot esse arquivo geralmente não é necessário, pois as configurações são feitas via `application.properties` ou `application.yml`.

---

## 10. `@NamedQuery`

O JPA permite definir queries JPQL diretamente na entidade, de forma nomeada e reutilizável:

```java
@Entity
@NamedQuery(name = "Produto.findByNome",
            query = "SELECT p FROM Produto p WHERE p.nome = :nome")
public class Produto {
    // ...
}

// Uso:
List<Produto> resultado = em
    .createNamedQuery("Produto.findByNome", Produto.class)
    .setParameter("nome", "Notebook")
    .getResultList();
```

---

## Resumo Geral das Especificações JPA:

| **Área** | **O que o JPA especifica** |
|---|---|
| Entidades | `@Entity`, `@Id`, `@Table`, `@Column`, `@Transient`, etc. |
| Relacionamentos | `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`, `mappedBy`, `cascade`, `fetch` |
| Ciclo de vida | Estados: Transient, Managed, Detached, Removed |
| Gerenciamento | Interface `EntityManager` e `EntityManagerFactory` |
| Transações | Interface `EntityTransaction` |
| Consultas | JPQL e `@NamedQuery` |
| Configuração | Arquivo `persistence.xml` e Unidade de Persistência |

---

> 🔹 **Lembre-se:** JPA é apenas a **especificação**. Quem executa de verdade é o **Framework ORM** (Hibernate, EclipseLink, etc.). No dia a dia com **Spring Boot**, o **Spring Data JPA** abstrai ainda mais essa camada, tornando a maior parte desse gerenciamento transparente para o desenvolvedor.
