# JDBC (Java Database Connectivity):  

### O que é?  

É uma API (biblioteca) nativa do Java que **ajuda na persistência de dados**, permitindo que sua aplicação se **conecte e interaja com um banco de dados relacional** (como MySQL, PostgreSQL, Oracle etc.).

- Disponibiliza conjunto de Classes e Interfaces para o acesso a Banco de Dados
- Se conecta a um driver de um SGBD específico que fornece a implementação do JDBC.

Basicamente o JDBC é a **forma mais pura e nativa de persistir dados**, onde atua como uma **camada de abstração entre o código Java e um banco de dados Relacional**. Ele permite que:

- Você escreva código Java **independente do banco usado.**
- Os drivers façam o trabalho sujo de se comunicar com o banco.


## Drivers JDBC:  
Basicamente um **Driver é uma implementação concreta dos contratos (classes e interfaces) fornecidos pelo JDBC**. Cada SGBD (MySQL, PostgreSQL, etc.) fornece uma implementação para o JDBC, 
servindo para **“traduzir”** os comandos genéricos do JDBC para o SGBD específico.

Ou seja:

- O **Java** usa o **JDBC** (interface)
- O **JDBC** se conecta com o **driver** (implementação)
- O **driver** se conecta com o **banco de dados**

OBS: Cada **Driver** dentro do Spring é disponibilizado como uma dependência (ou módulo), que deve ser declarada no pom.xml (caso estiver usando o Maven).

## Utilizando o JDBC:

### **1- Importar o pacote do Driver do SGBD que queira utilizar**

### **2- Carregar, registrar e criar uma conexão:**

```java
String url = "jdbc:postgresql://localhost:5432/testedb";
String username = "postgres";
String password = "123456";

Class.forName("org.postgresql.Driver");
Connection connection = DriverManager.getConnection(url, username, password);
```

### **3- Criar uma declaração:**

**3.1- criar uma declaração (*statement*):**

```java
Statement statement = connection.createStatement();
```

**3.2- criar uma consulta (*query)*:**

```java
String query = "SELECT name FROM student WHERE id_student = 1";
```

### **4- Executar a declaração:**

```java
statement.executeQuery(query);
```

A Interface **`Statement`** do JDBC disponibiliza diferentes **métodos para executar instruções SQL (query)**, **cada um para um tipo de consulta diferente (CREATE, READ, UPDATE, DELETE)**. Para cada tipo deve se usar um método diferente.

**Para consultas SELECT  usamos o `executeQuery()`**.

**4.2- armazenar os dados:**

Precisamos armazenar os dados em algum lugar. O método `executeQuery()` retorna um `ResultSet`, então:

```java
ResultSet resultSet = statement.executeQuery(query);
```

**4.3- verificar se a dados (registros) na tabela:**

A Interface `ResultSet` possui alguns métodos, e um deles é o `next()`:

```java
resultSet.next();
```

O método `next()` funciona da seguinte forma: Ele executa a *query*, e **se tiver uma linha seguinte (outro registro), ele retorna `true`, se não ele retornará `false`**.

Ou seja:

Se houver algum dado ele retornará `true`, caso contrário retornará `false`. 

```java
System.out.println(resultSet.next());
```

**4.4- imprimir os dados:**

Assim como o método `next()`, a Interface possui alguns métodos que servem para imprimir os dados, como:

`getString(String columnLabel)` → Esse método recebe como **parâmetro o nome da coluna e retorna o valor dessa coluna como uma *String***.

```java
resultSet.getString("name");
```

`getString(int columnindex)` → Tem a mesma finalidade, **porém recebe como parâmetro o índice que corresponde a coluna, o que não é muito intuitivo** (MENOS UTILIZADO)

```java
resultSet.getString(2);
```

Utilizando esses métodos podemos fazer uma simples impressão no console para teste:

```java
String name = resultSet.getString("name");
System.out.println("O nome do estudante é: " + name);
```

Porém ao tentar executar a aplicação, aparecerá o seguinte erro:

```java
Exception in thread "main" org.postgresql.util.PSQLException: ResultSet não está posicionado corretamente, talvez você precise chamar next.
```

Isso acontece porque a interface `ResultSet` funciona como um **cursor**, apontando para uma linha por vez dentro do conjunto de resultados retornados.

E por padrão, o cursor do `ResultSet` fica **posicionado antes da primeira linha**. Ou seja:

> ⚠️ Você ainda não está em uma linha válida para acessar dados!
> 

**“E porque o erro menciona a chamada do método `next()`?”**

O método `next()` **faz duas coisas importantes**:

1. **Move o cursor para a próxima linha do resultado** (inclusive a primeira).
2. **Retorna `true` se existir uma linha**, e `false` se não houver mais.

**“Então por que ocorre o erro ao chamar `getString()` antes de `next()`?”**

> **Porque você está tentando acessar os dados sem ter movido o cursor para uma linha válida.**
> 

✅Jeito certo:

```java
resultSet.next();
String name = resultSet.getString("name");
System.out.println("O nome do estudante é: " + name);
```

Resultado:

```
O nome do estudante é: Alessandro
```

### **5- fechar a conexão:**

```java
connection.close();
```

## Operações CRUD:

---

### **🔸Imprimindo todos os dados do banco (Read):**

Para qualquer tipo de operação, a base da **conexão** entre a aplicação e o banco é a mesma:

```java
String url = "jdbc:postgresql://localhost:5432/testedb";
String username = "postgres";
String password = "123456";
        
Class.forName("org.postgresql.Driver");
Connection connection = DriverManager.getConnection(url, username, password);
        
Statement statement = connection.createStatement();

connection.close();
```

Primeiro devemos mudar a consulta SQL (query):

```java
String query = "SELECT * FROM student";
```

Devemos criar uma variável do tipo `ResultSet` para armazenar o retorno da execução da query:

```java
ResultSet resultSet = statement.executeQuery(query);
```

Como o `ResultSet` funciona como um cursor, apontando para uma linha de cada vez, e não queremos fazer a mudança desse ponteiro manualmente, podemos usar um loop `while`para percorrer todas as linhas enquanto tiver uma próxima, usando o `next()`:

```java
while (resultSet.next()){
            
    System.out.print(resultSet.getInt("id_student") + " | ");
    System.out.print(resultSet.getString("name") + " | ");
    System.out.println(resultSet.getString("description") + " | ");
}
```

Resultado:

```
1 | Alessandro | Branco, gosta de fuma um baseado | 
2 | Maiquin do Pneu | Azul, gosta de bater em petista | 
3 | Xablau do Querô | Preto, gosta de jogar com os brother | 
```

### 🔸Inserir dados no banco (Create):

A primeira alteração que iremos fazer é na query, já que a consulta de Criação é diferente das demais consultas (Delete, Read, Update):

```java
String query = "INSERT INTO student (id_student, name, description) VALUES (4, 'Jonilson', 'Pardo, coleciona figurinha de Free Fire')";
```

Para a execução de query dos tipos **INSERT**, **UPDATE**, **DELETE**, **DDL** (como **CREATE TABLE**, **DROP TABLE**), podemos utilizar o método **`execute()`** ou o **`executeUpdate()`**.

```java
statement.execute(query);
//OU
statement.executeUpdate(query);
```

**`execute()`**:

É o método mais **genérico** da interface `Statement`. Ele pode ser usado para **qualquer tipo de comando SQL**

Retorno:

- `true`: Se a instrução executada retornar um objeto `ResultSet` (geralmente consultas **SELECT**).
- `false`: Se a instrução executada não retornar um `ResultSet`, como no caso de comandos de atualização (**INSERT**, **UPDATE**, **DELETE**, etc.).

Exemplo:

```java
String query = "INSERT INTO student (id_student, name, description) VALUES (4, 'Jonilson', 'Pardo, coleciona figurinha de Free Fire')";
System.out.println(statement.execute(query));
//Output: false
```

**`executeUpdate()`**:

Método **especializado** para comandos SQL que **modificam dados** (ou estrutura) no banco (**INSERT**, **UPDATE**, **DELETE**, **DDL**)

Retorno:

- Retorna um `int`, indicando o **número de linhas afetadas** pela operação.

Exemplo:

```java
String query = "INSERT INTO student (id_student, name, description) VALUES (4, 'Jonilson', 'Pardo, coleciona figurinha de Free Fire')";
System.out.println(statement.executeUpdate(query));
//Output: 1
```

### 🔸**Atualizar dados do Banco (Update):**

Como nas operações anteriores, o primeiro passo é alterar a query, de acordo com a operação que queremos realizar

Para atualizar o dado de um registro especifico por exemplo:

```java
String query = "UPDATE student SET name = 'Cleitin' WHERE id_student = 1";
```

Para as operações de modificação de dados (ou estruturas) a estrutura é a mesma.

Então se utilizarmos o método `execute()` e imprimi-lo terá a mesma saída das operações de Create ou Delete:

```java
String query = "UPDATE student SET name = 'Cleitin' WHERE id_student = 1";
System.out.println(statement.execute(query));
//Output: false
```

### **🔸Deletar dados do Banco:**

Alteramos a query:

```java
String query = "DELETE FROM student WHERE id_student = 5";
System.out.println(statement.execute(query));
//Output: false
```
