# Generics <T>

Funcionalidade que permite criar Classes, Interfaces e métodos **parametrizados**. Com isso você pode criar estruturas que podem trabalhar com diferentes tipos de dados não-primitivos sem ter que reescrever o código para cada tipo de dado.

> _"Estrutura parametrizada: significa que a estrutura genérica aceita **parâmetros de tipo**, onde você pode especificar o tipo específico de dados com os quais ela trabalhará."_

### Exemplos: 

Classe:
```
public class Box<T>{
}
```
Interface:
```
public interface Box<T>{
}
```
Método:
```
public <T> void openBox(){
}
```
```
public <T> void openBox(T item){
}
```
