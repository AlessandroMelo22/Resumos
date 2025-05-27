# Camadas do Spring Framework  
>  Este repositório contém resumos organizados por **grupos de módulos** do Spring Framework, para facilitar o estudo e a compreensão da arquitetura modular do Spring.

O Spring Framework é dividido em **módulos (dependências) que são agrupados por responsabilidade funcional**. 
Esses **grupos de módulos** formam a base para entender a arquitetura modular do Spring, como por exemplo o Core Container, responsável pela IoC e DI, e o grupo Web, que engloba módulos para aplicações web e REST.

---

## 📦 Core Container
Responsável pela Inversão de Controle (IoC) e Injeção de Dependência (DI):
- [`spring-core`](./Spring_Core_Container/1_spring-core.md)
- [`spring-beans`](./core-container/spring-beans.md)
- [`spring-context`](./core-container/spring-context.md)
- [`spring-context-support`](./core-container/spring-context-support.md)
- [`spring-expression`](./core-container/spring-expression.md)

---

## 🌀 AOP
Suporte à **programação orientada a aspectos**, para lidar com funcionalidades transversais (ex: logging, segurança):
- [`spring-aop`](./aop/spring-aop.md)
- [`spring-aspects`](./aop/spring-aspects.md)

---

## 💾 Data Access / Integration
Módulos voltados para **persistência de dados**, integração com bancos de dados, mensagens e outras tecnologias:
- [`spring-jdbc`](./data-access/spring-jdbc.md)
- [`spring-orm`](./data-access/spring-orm.md)
- [`spring-tx`](./data-access/spring-tx.md)
- [`spring-messaging`](./data-access/spring-messaging.md)
- [`spring-jms`](./data-access/spring-jms.md)
- [`spring-oxm`](./data-access/spring-oxm.md) (Indisponível)

---

## 🌐 Web
Recursos para desenvolvimento de **aplicações web e REST**:
- [`spring-web`](./web/spring-web.md)
- [`spring-webmvc`](./web/spring-webmvc.md)
- [`spring-websocket`](./web/spring-websocket.md)

---

## 🔐 Security
Responsável pela **autenticação e autorização** em aplicações Spring:
- [`spring-security-core`](./security/spring-security-core.md)
- [`spring-security-config`](./security/spring-security-config.md)
- [`spring-security-oauth2`](./security/spring-security-oauth2.md)
- [`spring-security-web`](./security/spring-security-web.md)
- [`spring-security-test`](./security/spring-security-test.md) (Indisponível)

---

## 🧪 Test
Ferramentas para **testes unitários e de integração** em aplicações Spring:
- [`spring-test`](./test/spring-test.md)
