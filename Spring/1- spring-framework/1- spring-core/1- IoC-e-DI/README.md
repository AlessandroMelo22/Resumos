# Inversão de Controle (IoC):

👉***É um princípio de design de código que visa tirar do programador o poder de criação e gerenciamento das instâncias das classes.***

O papel de um desenvolvedor deve estar centrado principalmente na **lógica de negócio (regra de negócio)**, e não na responsabilidade de criar e gerenciar o ciclo de vida dos objetos da aplicação. Para resolver isso, delegamos essa tarefa a outro componente, o que caracteriza a **Inversão de Controle (IoC — Inversion of Control)**.

### IoC Containers:
No Spring, essa responsabilidade fica a cargo dos **IoC Containers (ou Spring Containers, representados pelas Interfaces `BeanFactory` e `ApplicationContext`)**, que gerenciam todo o ciclo de vida dos objetos (*beans*), desde a criação até a destruição.

# Injeção de Dependências:
Para aplicar o princípio de IoC, o Spring utiliza o padrão de projeto conhecido como **Injeção de Dependências (DI — Dependency Injection)**. Nesse modelo, você apenas **declara quais dependências um objeto precisa, e o Spring se encarrega de fornecê-las**, evitando que o próprio código tenha que criá-las manualmente.

**Em resumo:**

- **IoC** é um **princípio de design** que propõe a inversão da responsabilidade de controle.
- **DI** é um **padrão de projeto** que implementa esse princípio na prática, no qual as dependências de um objeto são fornecidas e gerenciadas por um container.

> [!IMPORTANT]
> Vale lembrar que existem varias formas de implementar o princípio de **IoC (Inversion of Control)**, além da **DI (Dependency Injection)**, algumas delas são:
> - Service Locator
> - AOP (Aspect-Oriented Programming)
> - Tamplete method
> - Padrão de projeto Factory



Exemplo prático de IoC e DI:

```java
// Repositório simulado
@Repository
public class UserRepository {
    public String findUserName() {
        return "Maria Silva";
    }
}
```
```java
// Serviço que depende do UserRepository
@Service
public class UserService {

    private final UserRepository userRepository;

    // O Spring injeta automaticamente a dependência (UserRepository)
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public String getUserName() {
        return userRepository.findUserName();
    }
}

```

### Explicação:

- O **`UserRepository`** é um bean gerenciado pelo container do Spring (anotado com `@Repository`).
- O **`UserService`** depende do `UserRepository`.
- Em vez de criarmos manualmente o `UserRepository` com `new UserRepository()`, deixamos o **Spring Container** cuidar disso.
- O Spring detecta a dependência através do construtor e **injeta** o `UserRepository` dentro do `UserService`.
