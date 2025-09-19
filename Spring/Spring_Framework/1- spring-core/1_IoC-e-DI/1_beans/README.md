# Beans 
 

> ⚠Este diretório explica Beans no contexto de IoC/DI. Para detalhes técnicos do módulo spring-beans, consulte também

## O que é um Bean?  

**Objeto gerenciado pelo _Spring Container_**. Os conceitos de Inverção de Controle e Injeção de Dependências do _Spring Framework_ são aplicados apenas em objetos gerenciados pelo _Spring Container_, ou seja, em _Beans_.  

O _Spring Framework_ utiliza os Beans para desacoplar e organizar o código, tornando-o mais modular e reutilizável. Sem os _Beans_, teríamos que criar e gerenciar objetos manualmente em Java, o que aumenta a complexidade e acopla as classes.

## Como criar um Bean?

Para que um objeto seja considerado um Bean devemos marca-lo com uma _Annotation_, no Spring temos algumas opções para fazer isso:

### ▸ @Component  
É a forma mais simples de se criar um Bean, já que ao marcar uma classe com essa _annotation_ permite que o Spring ao realizar a varredura de componentes (_component scan_) detecte essa classe e a gerencie automaticamente. 

Outras _annotations_ como `@Service`, `@Repository` e `@Controller` são especializações de `@Component` com propósitos específicos, porém também servem para criar Beans de forma automática.

```
@Component
public class PagamentoCartao{

//corpo de código
}
```
