# Spring Configuration

*“refere-se a configuração do Spring Container”*  

Como foi citado, para implementar IoC e DI, o Spring disponibiliza dois **containers** principais, representados pelas interfaces **`BeanFactory`** e **`ApplicationContext`**. Esses containers são responsáveis por **instanciar, configurar, injetar dependências e gerenciar o ciclo de vida dos beans**. Para que eles saibam **como** realizar essas tarefas, é necessário fornecer **instruções** através de **metadados de configuração**, que podem ser definidos de diferentes formas: 

- **XML**
- **Classes Java com `@Configuration`**.
- A**notações**

A definição dessas **instruções**, que orientam o container sobre **como gerenciar os beans**, é chamada de ***Spring Configuration***.
> [!WARNING]
> Vale lembrar que o container **`BeanFactory`** suporta apenas configuração **baseada em XML**, já o **`ApplicationContext` todas as formas de configuração: XML, Annotation e Java**.


### Responsabilidades do **Spring Configuration**:

- Definir os Beans da aplicação
- Gerenciar as dependências entre os beans
- Definir ciclo de vida e escopos (*scope*) dos Beans
- Ler e aplicar propriedades externas (arquivos `application.properties` / `application.yml`)
