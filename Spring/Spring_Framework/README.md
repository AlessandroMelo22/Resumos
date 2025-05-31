# Camadas do Spring Framework  


O Spring Framework é dividido em **módulos (dependências) que são agrupados por responsabilidade funcional**. 
Esses **grupos de módulos** formam a base para entender a arquitetura modular do Spring, como por exemplo o Core Container, responsável pela IoC e DI, e o grupo Web, que engloba módulos para aplicações web e REST.

> Este repositório contém resumos organizados por **grupos de módulos** do Spring Framework. A ideia é entender como o Spring funciona **por baixo dos panos**, módulo por módulo, reforçando a base antes de avançar para o Spring Boot.

> Cada pasta representa um grupo de módulos com seus respectivos arquivos explicativos.

---

## 📦 Core Container
Responsável pela Inversão de Controle (IoC) e Injeção de Dependência (DI):
- [`spring-core`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/1-Spring_Core_Container/1_spring-core.md)
- [`spring-beans`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/1-Spring_Core_Container/2_spring-beans.md)
- [`spring-context`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/1-Spring_Core_Container/3_spring-context.md)
- [`spring-expression`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/1-Spring_Core_Container/4_spring-expression.md)

---

## 🌀 AOP
Suporte à **programação orientada a aspectos**, para lidar com funcionalidades transversais (ex: logging, segurança):
- [`spring-aop`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/2-Aspect_Oriented_Programmig_AOP_e_Instrumentation/1_spring-aop.md)
- [`spring-aspects`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/2-Aspect_Oriented_Programmig_AOP_e_Instrumentation/2_spring-aspects.md)

---

## 💾 Data Access / Integration
Módulos voltados para **persistência de dados**, integração com bancos de dados, mensagens e outras tecnologias:
- [`spring-jdbc`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/3-Data_Acess_e_Integration/1_spring-jdbc.md)
- [`spring-orm`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/3-Data_Acess_e_Integration/2_spring-orm.md)
- [`spring-tx`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/3-Data_Acess_e_Integration/3_spring-tx.md)
- [`spring-messaging`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/3-Data_Acess_e_Integration/4_spring-messaging.md)
- [`spring-jms`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/3-Data_Acess_e_Integration/5_spring-jms.md)
- [`spring-oxm`](./data-access/spring-oxm.md) (Indisponível)

---

## 🌐 Web
Recursos para desenvolvimento de **aplicações web e REST**:
- [`spring-web`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/4-Web_e_MVC/1_spring-web.md)
- [`spring-webmvc`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/4-Web_e_MVC/2_spring-webmvc.md)
- [`spring-webfux`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/4-Web_e_MVC/3_spring-webflux.md)
- [`spring-websocket`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/4-Web_e_MVC/4_spring-websocket.md)

---

## 🔐 Security
Responsável pela **autenticação e autorização** em aplicações Spring:
- [`spring-security-core`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/5-Security/1_spring-security-core.md)
- [`spring-security-config`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/5-Security/2_spring-security-config.md)
- [`spring-security-oauth2`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/5-Security/3_spring-security-oauth2.md)
- [`spring-security-web`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/5-Security/4_spring-security-web.md)
- [`spring-security-test`](./security/spring-security-test.md) (Indisponível)

---

## 🧪 Test
Ferramentas para **testes unitários e de integração** em aplicações Spring:
- [`spring-test`](https://github.com/AlessandroMelo22/Resumos/blob/main/Spring/Spring_Framework/6-Test/1_spring-test.md)
