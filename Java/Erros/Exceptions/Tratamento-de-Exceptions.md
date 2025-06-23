# Tratamento de Exceções no Java
O tratamento de execeções é o processo de lidar com **situações inesperadas ou erros que podem ocorrer durante a execução de um programa Java**. 
O objetivo principal é **evitar que o programa finalize abruptamente** e permitir que ele continue funcionando, ou pelo menos forneça uma mensagem de erro adequada ao usuário. 

Ou seja:  

**✅Tratar os erros do Java, permite que o programa execute uma determinada lógica caso o erro ocorra, além de fazer com que o programa continue a execução.** 

**❌Não tratar os erros causa a interrupção do programa, prejudicando a experiencia do usuário final.**



---

### 🔸`try-catch`

Basicamente funciona da seguinte forma:  
Dentro do `try` fica o **bloco de código que pode gerar uma Exception**, enquanto o `catch` **fica responsável por capturar e tratar essa Exception**:

```  
try {

  //BLOCO DE CÓDIGO QUE É MONITORADO PARA ERROS

} catch(TipoDaException exception) {

  //TRATAMENTO DO ERRO

}

```
---

### 🔸Múltiplos `catch`  
Funciona da mesma forma que o `try catch` convencional porém **é utilizado quando um bloco de código pode gerar mais de uma Exception**:

```
try {

  //BLOCO DE CÓDIGO QUE É MONITORADO PARA ERROS

} catch(TipoDaException1 exception1) {

  //TRATAMENTO DO ERRO 1

} catch(TipoDaException2 exception2) {

  //TRATAMENTO DO ERRO 2

}
```

---

### 🔸Mútiplos `catch` de Exceptions da mesma família  

Quando temos que tratar **Exceptions que pertencem a mesma família**, ao invés de utilizar vários blocos `catch` seguidos, podemos utilizar apenas um bloco `catch` e declarar todas as Exceptions seguindo a seguinte sintaxe:  

```
try {

  //BLOCO DE CÓDIGO QUE É MONITORADO PARA ERROS

} catch(TipoDaException1 exception1 | TipoDaException2 exception2 | TipoDaException3 exception3) {

  //TRATAMENTO DO ERRO

}
```
---

### 🔸`try-catch-finally`  

O bloco `finally` em Java **é usado para garantir que um determinado bloco de código seja executado, independentemente de ocorrer uma exceção ou não dentro do bloco** `try`. É frequentemente **utilizado para liberar recursos, como fechar arquivos ou conexões de banco de dados**, garantindo que esses recursos sejam sempre liberados, mesmo que ocorra um erro:

```  
try {

  //BLOCO DE CÓDIGO QUE É MONITORADO PARA ERROS

} catch(TipoDaException exception) {

  //TRATAMENTO DO ERRO

} finally {

  //EXECUTADO APÓS O BLOCO TRY OU CATCH

}
```
**OBSERVAÇÃO:** Como foi mencionado, o bloco `finally` é executado SEMPRE, mesmo se houver um erro ou não, porém a uma exceção, caso seja passado o método `System.exit(int status)` dentro do bloco `catch`, e o mesmo for executado, o bloco `finally` e qualquer coisa que esteja depois não serão executados:  

```
try {

  //BLOCO DE CÓDIGO QUE IRÁ GERAR UM ERRO

} catch(TipoDaException exception) {

  //TRATAMENTO DO ERRO
  System.exit(0);

} finally {

  //ESSE BLOCO NUNCA SERÁ EXECUTADO

}

System.out.println("ESSE COMANDO NUNCA SERÁ EXECUTADO");

```
---
### 🔸Métodos da super classe `Throwable` utilizados no tratamento de Exceptions:  

`getMessage()` ➡ retorna a descrição do erro.  
`printStackTrace()` ➡ imprime a _stack trace_ do erro

---

### `throws`

A palavra-chave `throws` é **usada na declaração de um método para indicar que esse método pode lançar uma ou mais exceções**. Em outras palavras, **ela informa ao código que chama esse método que ele deve estar preparado para lidar com a possibilidade de uma exceção ocorrer durante a execução do método**:

