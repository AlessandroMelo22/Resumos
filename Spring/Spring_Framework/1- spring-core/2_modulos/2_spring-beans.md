# `spring-beans`  

### ⚙️ Função:
O módulo `spring-beans` fornece **um dos Contêineres básicos do Spring**, além das ferramentas (classes, interfaces, etc.) usadas pelos contêineres para **implementar Inversão de Controle (IoC) e Injeção de Dependências (DI), gerenciando o ciclo de vida dos Beans**.

---

### 🛠 Recursos disponíveis:

`BeanFactory`: um dos Contêineres básicos do Spring, responsável de fato por gerenciar a criação, configuração e ciclo de vida dos objetos (Beans) da aplicação.

`@Autowired`: anotação usada para que o Spring resolva e injete automaticamente uma dependência em um Bean.

`@Qualifier`: usada junto com @Autowired para especificar qual implementação deve ser injetada quando houver múltiplas opções do mesmo tipo.

Permite definir escopos de Beans, como:
- singleton (um único Bean compartilhado)
- prototype (um novo Bean a cada solicitação)
- entre outros (como request, session, etc., em contextos web)

---

### Dependência dentro do `pom.xml`:

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>6.2.5</version>
</dependency>
```

---

### ❌Sem o `spring-beans`: 

- O `BeanFactory` e suas implementações não existem;
- As anotações como `@Autowired` e `@Qualifier` não funcionam;
- Você perde a funcionalidade central de IoC (Inversão de Controle) e DI (Injeção de Dependência) no Spring.

