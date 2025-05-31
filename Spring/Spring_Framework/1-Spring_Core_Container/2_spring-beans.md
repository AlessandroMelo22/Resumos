# `spring-beans`  

### ⚙️ Função:
O módulo `spring-beans` fornece **um dos Contêineres básicos do Spring**, além das ferramentas (classes, interfaces, etc.) usadas pelos contêineres para **implementar Inversão de Controle (IoC) e Injeção de Dependências (DI), gerenciando o ciclo de vida dos Beans**.

---

### Recursos disponiveis:

`BeanFactory`: Um dos Contêineres básicos do Spring, responsavel de fato por gerenciar a criação, configuração e ciclo de vida dos objetos (Beans) da aplicação.

`@Autowired`: anotação usada para que o Spring resolva e injete automaticamente uma dependência em um Bean.

`@Qualifier`: usada junto com @Autowired para especificar qual implementação deve ser injetada quando houver múltiplas opções do mesmo tipo.


