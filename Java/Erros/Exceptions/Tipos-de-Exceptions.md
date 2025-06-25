# Tipos de Exceptions

No Java as Exceptions são classificadas em:  

🟢 Checked Exceptions  
🔴 Unchecked Exceptions  

---

### 🟢 Checked Exceptions

São exceções que o compilador **OBRIGA** você a **tratar (com `try-catch`) ou propagar (com `throws`) na assinatura do método**, além disso são exceções que herdam diretamente da classe `Exception`.  

As Checked Exceptions se referem ao **erros esperados e recuperáveis** (_erros que podem ser previstos durante a execução do programa e que podem ser tratadas de forma segura e controlada_).

Exemplos de Checked Exceptions:  
- O usuário tenta abrir um arquivo que não existe ➡ `IOException`
- A conexão com o banco de dados falha temporariamente ➡`SQLException`
- O cliente envia uma data mal formatada ➡`ParseException`

Exemplo de código:  

```
public void lerArquivo(String caminho) throws IOException {

    BufferedReader leitor = new BufferedReader(new FileReader(caminho)); // Pode lançar IOException
}
```
