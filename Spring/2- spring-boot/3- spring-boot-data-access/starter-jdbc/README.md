# `spring-boot-starter-jdbc`

## 1️⃣ O que o `spring-boot-starter-jdbc` fornece?

Esse starter do Spring Boot adiciona ao projeto **tudo o que você precisa para trabalhar com JDBC**, mas sem um ORM. Ele inclui:

| Item | Descrição |
| --- | --- |
| **Spring JDBC** (`spring-jdbc`) | Abstrações para trabalhar com banco de dados usando JDBC, mas com menos código que o JDBC “puro” |
| **`JdbcTemplate`** | Objeto principal para executar SQL (SELECT, INSERT, UPDATE, DELETE) |
| **Tratamento de erros** | Conversão das exceções de SQL para exceções do Spring (ex.: `DataAccessException`) |
| **Suporte a DataSource** | Configura automaticamente a conexão com o BD via `application.properties` |
| **Connection Pool** | Auto-configura um pool de conexões (HikariCP) |

> [!WARNING]
> Ele **NÃO fornece mapeamento automático de entidades** como o `spring-boot-starter-data-jpa`.


## 2️⃣ Quais recursos ele oferece para fazer um CRUD?

Com o `JdbcTemplate`, você consegue:

### 🔹 Inserir

```java
String sql = "INSERT INTO usuario (nome, email) VALUES (?, ?)";
jdbcTemplate.update(sql, usuario.getNome(), usuario.getEmail());
```

### 🔹 Consultar (SELECT)

```java
String sql = "SELECT * FROM usuario WHERE id = ?";
return jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(Usuario.class), id);
```

### 🔹 Atualizar

```java
String sql = "UPDATE usuario SET nome = ? WHERE id = ?";
jdbcTemplate.update(sql, novoNome, id);
```

### 🔹 Deletar

```java
String sql = "DELETE FROM usuario WHERE id = ?";
jdbcTemplate.update(sql, id);
```

👉 Ou seja, você usa **SQL na mão**, mas com o conforto do Spring gerenciando conexões e exceções.


## 3️⃣ Como funciona a conexão com o banco de dados?

No `application.properties` (ou no `application.yml`) você configura:

```
spring.datasource.url=jdbc:mysql://localhost:3306/mdm_db
spring.datasource.username=root
spring.datasource.password=1234
```

O Spring Boot automaticamente:

- cria um **DataSource**
- configura o **HikariCP** (pool de conexões)
- entrega um `JdbcTemplate` pronto para usar

Você só injeta:

```java
@Autowired
private JdbcTemplate jdbcTemplate;
```

## 4️⃣ Como funciona a criação de tabelas?

O starter **não cria tabelas automaticamente**.

Você tem três opções:

| Abordagem | Como funciona |
| --- | --- |
| Manual | Você mesmo cria tabelas com SQL |
| `schema.sql` | Coloca um script SQL no diretório `resources` e o Spring executa ao iniciar |
| Flyway/Liquibase (recomendado) | Controle de versão para scripts de banco |

Exemplo com `schema.sql`:

```sql
CREATE TABLE usuario (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL
);
```

## Vantagens de usar **somente** o `spring-boot-starter-jdbc`

✅ **Performance** — mais rápido que JPA/Hibernate (por não ter um ORM no meio)

✅ **Mais controle sobre SQL** — ideal para consultas específicas e tunadas

✅ **Mais simples para quem está aprendendo como o banco funciona**

✅ **Menos “mágica”** — você vê exatamente o SQL que está rodando

---

## ⚠️ Desvantagens e limitações

❌ Você escreve SQL manualmente — maior esforço

❌ Não há mapeamento automático de entidades

❌ Não tem gerenciamento automático de relacionamento entre tabelas

❌ Mais código boilerplate quando o projeto cresce

Se seu projeto cresce ou precisa de muito relacionamento entre entidades, JPA vira vantagem.
