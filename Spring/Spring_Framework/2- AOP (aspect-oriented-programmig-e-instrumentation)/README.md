# AOP (Programação Orientada a Aspectos)

AOP é um paradigma de programação que visa aumentar a modularidade, permitindo a separação de preocupações transversais da lógica de negócio. Isso é feito adicionando comportamento adicional ao código existente sem modificar o código (lógica de negócio) em si. 

 

### Preocupações transversais são funcionalidades que: 

- Não fazem parte da lógica principal (a regra de negócio), 
- Mas que são necessárias e ocorrem em vários pontos da aplicação, 
- E que, se feitas "manualmente", ficariam espalhadas (duplicadas) pelo sistema todo. 

 

### Exemplos de preocupações transversais: 

- Logs; 
- Validações; 
- Tratamento de exceções; 
- Autenticação e autorização; 
- Transações; 
- Medição de desempenho (tempo de execução); 

### Como o AOP resolve esse problema? 

Com AOP, você separa essas preocupações transversais em um "aspecto". O aspecto intercepta a execução dos métodos e injeta comportamento extra antes, depois ou ao redor da execução do método original 

 
 

## Conceitos importantes da AOP: 

Aspect ➡ Classe que contém as preocupações transversais (log, transações, etc..). 

Advice ➡ A ação (código/método) que será executada em um Join Point. Ex: before, after, around. 

Join Point ➡ É um ponto específico durante a execução do programa onde você pode inserir um Advice (No Spring sempre será “ao executar um método”). 

Pointcut ➡ É a expressão (predicato) que seleciona quais Join Points serão afetados pelo Advice. 

Weaving ➡ Processo de aplicar (injetar) o aspecto no código alvo. 
