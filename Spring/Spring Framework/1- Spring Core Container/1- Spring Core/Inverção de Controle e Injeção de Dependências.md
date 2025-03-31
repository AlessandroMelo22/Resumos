# Inversão de Controle (IoC)  

É um princípio de design de código que visa tirar do programador o poder de criação e gerenciamento das instâncias das classes. Com isso essa responsabilidade passa a ser de um container (como o Spring).  

➡ Antes da IoC: A própria classe cria e gerencia suas dependências.  
➡ Com IoC: Um container (como o Spring) gerencia a criação e a injeção dos objetos.  

# Injeção de Dependências

A DI ocorre quando **um objeto recebe suas dependências de fora, invés de cria-las internamente**. No _Spring Framework_, o DI é uma das principais funcionalidades do container IoC (Inversion of Control). 

# Funcionamento
A criação do Spring Framework veio para simplificar o desenvolvimento de aplicações Java, promovendo a Inversão de Controle (IoC) e Injeção de Dependências (DI). Com esses conceitos os desenvolvedores podem se concentrar na lógica de negócios enquanto o framework cuida da criação e gerenciamento dos objetos (Beans) necessários para a aplicação.
