# Tratamento de Exceções no Java
O tratamento de execeções é o processo de lidar com **situações inesperadas ou erros que podem ocorrer durante a execução de um programa Java**. 
O objetivo principal é **evitar que o programa finalize abruptamente** e permitir que ele continue funcionando, ou pelo menos forneça uma mensagem de erro adequada ao usuário. 

Ou seja:  

**✅Tratar os erros do Java, permite que o programa execute uma determinada lógica caso o erro ocorra, além de fazer com que o programa continue a execução.** 

**❌Não tratar os erros causa a interrupção do programa, prejudicando a experiencia do usuário final.**



---

### `try catch`

Basicamente funciona da seguinte forma:  
Dentro do `try` fica o **bloco de código que pode gerar uma Exception**, enquanto o `catch` **fica responsável de capturar e tratar essa Exception**:

```  
try {

  //BLOCO DE CÓDIGO QUE É MONITORADO PARA ERROS

} catch(TipoDaException exception) {

  //TRATAMENTO DO ERRO

}

```
---

### Múltiplos `catch`  
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
