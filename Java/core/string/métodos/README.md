# Métodos para trabalhar com String

### 1️⃣ `charAt(int index)`

→ retorna o `char` baseado no índice

Exemplo:
```java
String name = "Alessandro";
System.out.println(name.charAt(0));

//SAÍDA: A
```
### 2️⃣ `replace()`

→ percorre a String e troca a letra ou sequência de caracteres que for passada no primeiro parâmetro pela letraou sequência de caracteres que for passada no segundo parâmetro. 

Exemplo:

`replace(char oldChar, char newChar)` → Substitui todas as ocorrências de um **caractere** (`oldChar`) por outro (`newChar`)

```java
String name = "gato preto";
System.out.println(name.replace('o', 'a'));

//SAÍDA: gata preta
```

`replace(CharSequence target, CharSequence replacement)` → Substitui todas as ocorrências de uma **sequência de caracteres (`target`)** por outra (`replacement`). 
`CharSequence` é uma interface que String implementa, permitindo o uso de strings como parâmetros.

```java
String name = "gato preto";
System.out.println(name.replace("gato", "cachorro" ));

//SAÍDA: cachorro preto
```

### 3️⃣ `toLowerCase()`
→ passa a String para **caixa baixa**. 

Exemplo:
```java
String name = "GATO PRETO";
System.out.println(name.toLowerCase());

//SAÍDA: gato preto
```

### 4️⃣ `toUpperCase()`
→ passa a String para **caixa alta**.

Exemplo:
```java
String name = "gato preto";
System.out.println(name.toUpperCase());

//SAÍDA: GATO PRETO
```

### 5️⃣ `substring(int beginIndex, int endIndex)`
→ extrai um pedaço de uma string, retornando uma nova string que contém os caracteres dentro de um intervalo de índices especificado.

A string que será retornada começa a partir do **primeiro índice (`beginIndex`)** e vai até o **último indice (`endIndex`) -1**:

Exemplo:
```java
String name = "lelek";
System.out.println(name.substring(0, 4)); // último indice é 4, então vai de 0 ao índice 3

//SAÍDA: lele
```

### 6️⃣ `trim()`
→ remove espaços em branco (incluindo espaços, tabulações e quebras de linha) do início e do final de uma String

Exemplo:
```java
String name = "   lelek     ";
System.out.println(name.trim());

//SAÍDA: lelek
```
> [!WARNING]
> Ele **NÃO** remove espaços em branco que estão no **meio** da string. 
```java
String name = "   le   lek     ";
System.out.println(name.trim());

//SAÍDA: le   lek
```

### 7️⃣ `contains(CharSequence s)`
→ verifica se a string **contém uma substring específica**, retornando **`true` se a substring for encontrada e `false` caso contrário**.

Exemplo:
```java
String name = "banana";
System.out.println(name.contains("ana"));

//SAÍDA: true
```

### 8️⃣ `indexOf()`
→ usado para encontrar o **índice da primeira ocorrência de um caractere ou substring dentro de uma string**.

Exemplo:

`indexOf(Char c)`
```java
String name = "banana";
System.out.println(name.indexOf('a'));

//SAÍDA: 1
```

`indexOf(String str)`
```java
String name = "banana";
System.out.println(name.indexOf("nana"));

//SAÍDA: 2
```
```java
String name = "banana";
System.out.println(name.indexOf("ão"));

//SAÍDA: -1
```
