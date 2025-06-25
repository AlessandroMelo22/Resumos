# Tipos de Exceptions

No Java as Exceptions são divididas em:  

🟢 Checked Exceptions  
🔴 Unchecked Exceptions  

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

São exceções que **NÃO obrigam** você a **tratar ou propagar** e que acontecem em tempo de execução, além disso são exceções que herdam da classe `RunTimeException` (subclasse de `Exception`).  

As **Unchecked Exceptions** se referem aos **erros não esperados e inrrecuperáveis** (_indicam erros na lógica de programação ou falhas no código_).

Exemplo de Unchecked Exceptions:
- Você tenta acessar um objeto que é null ➡ `NullPointerException`
- Divide por zero ➡ `ArithmeticException`
- Acessa índice inválido em array ➡ `ArrayIndexOutOfBoundsException`

Esses erros não deveriam acontecer se o código estivesse certo. Logo, não faz sentido forçar tratamento.

Exemplo de código:
```
public void imprimirNome(String nome) {
    System.out.println(nome.toUpperCase()); // Pode lançar NullPointerException
}
```
🧠 _Se `nome` for `null`, é um erro de lógica do programador — ele deveria ter garantido que nome nunca fosse nulo antes disso._

_Você não recupera isso com elegância; precisa consertar o código._
