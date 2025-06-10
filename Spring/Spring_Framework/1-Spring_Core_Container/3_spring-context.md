# `spring-context`  

### ⚙ Função:  
Expande o módulo `spring-beans`, oferecendo um gerenciamento de Beans mais completo e integrando recursos essenciais ao desenvolvimento de aplicações.  

---

### 🛠 Recursos disponíveis:  

Ele fornece o `ApplicationContext`, que é o **contêiner principal utilizado na maioria das aplicações Spring**, estendendo o `BeanFactory` (do módulo `spring-beans`) e adicionando funcionalidades mais avançadas para aplicações reais, como por exemplo:

- **Inicialização Eager Loading**: os Beans são inicializados no momento em que o contexto é carregado;

- **Suporte a eventos**: permite a publicação e o consumo de eventos dentro da aplicação;

- **Internacionalização**: suporte a mensagens e traduções para múltiplos idiomas;

- **Integração facilitada com outros módulos do Spring**: como Spring AOP, Spring Security, entre outros.

---

### Dependência dentro do `pom.xml`:

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>6.2.5</version>
</dependency>
```
---

### ❌Sem o `spring-context`:  

Se você **não utilizar** o módulo `spring-context`, sua aplicação continuaria podendo gerenciar Beans, **mas com recursos limitados**, pois estaria usando apenas o `BeanFactory` (do `spring-beans`).
