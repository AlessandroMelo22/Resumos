# Maven:  

Basicamente o Maven é um gerenciador de dependências e ciclo de vida de projetos Java.

## Funcionalidades:

### 1️⃣ Gerenciamento de Dependências:

  Com o Maven se torna possivel adicionar bibliotecas externas ao projeto com apenas algumas configurações no arquivo `pom.xml`. Ele busca essas bibliotecas automaticamente de repositórios online (como o _Maven Central_) ou no repositório local (_.m2/repository_) caso ele já tenha baixado anteriormente. 
 Sendo assim o Maven fica responsável por **baixar, armazenar e resolver as bibliotecas necessárias para o projeto**, ele também **toma conta das dependências transitivas (dependência de uma dependência)**. 

Ex:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <verision>3.1.0</version>
</dependency>
```
 Sem o Maven seria necessário baixar manualmente os arquivos `.jar` e configurar manualmente no `classpath`. 

> OBSERVAÇÂO:
 O maven possui um conjunto de regras para escolher qual versão de uma dependência será usada quando há múltiplas versões declaradas direta ou indiretamente (por herança ou dependências transitivas).

> Porém vale ressaltar que o Maven por si só **não garante a compatibilidade entre versões de diferentes dependências** (uma versão de uma dependência pode ter incompatibilidade com a versão de outra dependência). 
 
