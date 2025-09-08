# Error:  

**Representa problemas sérios e irrecuperáveis no sistema, como falta de memória, falhas na JVM, problemas de hardware ou qualquer condição que esteja fora do controle do programa**. 

**Esses erros não são possíveis de serem tratados pelo nosso programa**. 

 
Exemplos: 

- `StackOverflowError` → acontece quando a pilha de execução (stack) fica cheia, geralmente devido a chamadas recursivas infinitas ou muito profundas.
- `OutOfMemoryError` → ocorre quando a JVM não consegue mais alocar memória para novos objetos.
- `NoClassDefFoundError` → lançado quando a JVM não consegue carregar uma classe necessária em tempo de execução.
- `UnsatisfiedLinkError` → ocorre quando a JVM não encontra uma implementação nativa de um método declarado como native.
- `AssertionError` → lançado quando uma asserção (`assert`) falha durante a execução.

 
