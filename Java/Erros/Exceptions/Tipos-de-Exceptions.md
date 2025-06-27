# Tipos de Exceptions

No Java as Exceptions são divididas em:  

🟢 Checked Exceptions  
🔴 Unchecked Exceptions  

🎨 Custom Exceptions

---

### 🟢 Checked Exceptions

São exceções que o compilador **OBRIGA** você a **tratar (com `try-catch`) ou propagar (com `throws`) na assinatura do método**, além disso são exceções que herdam diretamente da classe `Exception`.  

As **Checked Exceptions** se referem aos **erros esperados e recuperáveis** (_erros que podem ser previstos durante a execução do programa e que podem ser tratadas de forma segura e controlada_).

Exemplos de Checked Exceptions:  
- O usuário tenta abrir um arquivo que não existe ➡ `IOException`
- A conexão com o banco de dados falha temporariamente ➡`SQLException`
- O cliente envia uma data mal formatada ➡`ParseException`

Exemplo de código:  

```
public void carregarArquivo(String caminho) throws FileNotFoundException {
    File file = new File(caminho);
    Scanner sc = new Scanner(file); // Pode lançar FileNotFoundException
}
```
🧠  _Pode acontecer, é normal — você pode:_

_- Avisar o usuário_  
_- Pedir outro caminho_  
_- Criar o arquivo se ele não existir_  


---

### 🔴 Unchecked Exceptions

São exceções que o compilador **NÃO obriga** você a **tratar ou propagar**, e que acontecem em tempo de execução, além disso são exceções que herdam da classe `RunTimeException` (subclasse de `Exception`).  

As **Unchecked Exceptions** se referem aos **erros não esperados e inrrecuperáveis** (_indicam erros na lógica de programação ou falhas no código_).

Exemplo de Unchecked Exceptions:
- Você tenta acessar um objeto que é null ➡ `NullPointerException`
- Divide por zero ➡ `ArithmeticException`
- Acessa índice inválido em array ➡ `ArrayIndexOutOfBoundsException`

⚠ Esses erros não deveriam acontecer se o código estivesse certo. Logo, não faz sentido forçar tratamento, por isso são **"inrrecuperáveis"**.

Exemplo de código:
```
public void imprimirNome(String nome) {
    System.out.println(nome.toUpperCase()); // Pode lançar NullPointerException
}
```
🧠 _Se `nome` for `null`, é um erro de lógica do programador — ele deveria ter garantido que nome nunca fosse nulo antes disso._

_Você não recupera isso com elegância; precisa consertar o código._

---

### 🎨 Custom Exceptions

No Java é possivel que o desenvolvedor **crie suas próprias exceções**, sendo uma ferramenta poderosa **para melhorar o tratamento de erros**.  

Basicamente podemos criar tipos de exceções específicas para condições de erro exclusivas de uma aplicação.

✔ Benefícios:  
- **Código mais legível** ➡ Exceções personalizadas tornam o código mais claro e fácil de entender, pois a mensagem de erro reflete o problema específico. 
- **Melhor manutenção** ➡ Ao usar exceções personalizadas, a manutenção do código se torna mais eficiente, pois os erros são mais facilmente localizados e corrigidos. 
- **Informações adicionais** ➡ Permitem adicionar informações adicionais sobre o erro, como dados relevantes para o tratamento do problema. 
- **Flexibilidade** ➡ Permitem tratar erros de forma diferente dependendo do contexto da aplicação. 
- **Padronização de mensagens** ➡ Permitem padronizar mensagens de erro para a aplicação, garantindo consistência.

```
public class SaldoInsuficienteException extends Exception {
    public SaldoInsuficienteException(String message) {
        super(message);
    }
}
```
```
public class Conta {
    private double saldo;

    public void sacar(double valor) throws SaldoInsuficienteException {
        if (valor > saldo) {
            throw new SaldoInsuficienteException("Saldo insuficiente para realizar o saque.");
        }
        saldo -= valor;
    }
}
```
