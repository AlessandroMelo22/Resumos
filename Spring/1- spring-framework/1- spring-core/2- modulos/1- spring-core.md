# `spring-core`

### ⚙Função: 
Fornece a **infraestrutura** fundamental para que os **conceitos de Inversão de Controle (IoC) possam ser implementados no Spring**.
- Ele **não implementa diretamente** o ciclo de vida dos beans nem realiza injeção de dependências, mas **oferece a base sobre a qual os containers Spring (`BeanFactory` ou `ApplicationContext` por exemplo) constróem essas funcionalidades**.
- Inclui classes utilitárias, como `ReflectionUtils`, `Assert`, `Resource`, e mecanismos de manipulação de propriedades (`PropertyResolver`, `PropertyEditor`), entre outros.

---
### Dependência dentro do `pom.xml`:

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>6.2.5</version>
</dependency>
```
---

### ❌Sem o `spring-core`:   
Os containers do Spring não teriam acesso às ferramentas essenciais — como utilitários de reflexão, manipulação de recursos e suporte ao sistema de propriedades — necessárias para implementar IoC e DI.
