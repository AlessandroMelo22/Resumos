# Configuração baseada em anotações:


A configuração baseada em anotações trouxe uma forma **declarativa e enxuta de configurar o container IoC**.
Em vez de declarar **manualmente** cada bean (`@Configuration` + `@Bean`), o desenvolvedor pode simplesmente **anotar classes com estereótipos como `@Component`, `@Service`, `@Repository` ou `@Controller`**. 

Combinada ao `@ComponentScan`, o Spring passa a **detectar automaticamente essas classes durante a inicialização**, criando os beans de forma automática.
Essa abordagem reduz o código de configuração e aproxima ainda mais a configuração da lógica de negócio.


> [!IMPORTANT]
> **O Spring faz o trabalho de registrar beans automaticamente com base em anotações colocadas nas classes.
O desenvolvedor não precisa declarar cada bean manualmente — basta anotar as classes corretas.**
>
> É o modelo de configuração que prevalece até a **Configuração Automática do Spring Boot (`@SpringBootApplication` + Auto Configuration)**.




**Vantagens**:

✅ Reduz código de configuração manual  
✅ Facilita a criação rápida de projetos  
✅ Mantém a configuração próxima do código de negócio

**Desvantagens**:

❌ Pode gerar menos controle explícito sobre os beans criados  
❌ Dificulta o rastreamento de dependências em sistemas muito grandes



# Exemplo de como trabalhar com configuração baseada em Anotações


## 1️⃣ Criação do nosso POJO:
Para os exemplos, imaginemos que temos um POJO do tipo `Desktop`
