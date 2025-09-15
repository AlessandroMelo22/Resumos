# Definição da FK dentro do relacionamento:

Na modelagem lógica, além de definir chaves primárias, tipos de dados e restrições, também é necessário estabelecer em qual tabela ficará a chave estrangeira (FK) de um relacionamento. 
Esse processo é conhecido como a definição do lado proprietário da FK.  
Para isso, existe uma espécie de “regra prática” que orienta a alocação conforme o tipo de relacionamento:

## 1:1

Quando temos um relacionamento 1:1, e não houver obrigatoriedade em nenhuma das tabelas, a chave Estrangeira pode ser adicionada a qualquer uma das tabelas, mas não nas duas. 
