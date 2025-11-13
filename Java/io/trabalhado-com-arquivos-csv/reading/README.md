# Leitura (Reading):

## Componentes usados para a Leitura de arquivos:

## 🔹 `File`

→ Classe que **representa o arquivo** dentro do sistema operacional. **Não lê o arquivo**, apenas aponta onde ele está

**Características:**

- Contém informações sobre o arquivo (**nome**, **caminho**, **tamanho**, etc.)
- Serve como **ponto de entrada** para manipulação de arquivos
- Permite tratar erros ANTES de tentar ler

**Pontos a se atentar:**

- Não garante que o arquivo existe — é necessário validar
- É comum usá-lo apenas como referência; a leitura real é feita por streams/readers

Principais métodos utilizados:

| Método | Descrição | Exemplo |
| --- | --- | --- |
| `exists()` | Verifica se o arquivo ou diretório existe | `file.exists()` |
| `canRead()` | **verificar se o programa tem permissão para ler o arquivo ou diretório** | `file.canRead()` |
| `createNewFile()` | Cria um novo arquivo vazio | `file.createNewFile()` |
| `delete()` | Deleta o arquivo/diretório | `file.delete()` |
| `mkdir()` | Cria um diretório | `file.mkdir()` |
| `mkdirs()` | Cria diretórios, incluindo pais necessários | `file.mkdirs()` |
| `getName()` | Retorna somente o nome do arquivo | `file.getName()` |
| `getAbsolutePath()` | Caminho absoluto do arquivo | `file.getAbsolutePath()` |
| `length()` | Tamanho do arquivo em bytes | `file.length()` |
| `list()` | Lista arquivos dentro de um diretório | `file.list()` |
| `isFile()` | Verifica se é arquivo | `file.isFile()` |
| `isDirectory()` | Verifica se é diretório | `file.isDirectory()` |

## 🔹 `FileReader`

→ Classe **base** que permite **ler arquivos, caractere por caractere**.

Responsável por ler os ***bytes* do arquivo e transformar em caracteres (`char`).**

**Características:**

- É um leitor (subclasse de `Reader`).
- Serve **apenas** para leitura de **arquivos de texto**.
- Não possui *buffer*, então ler diretamente dele é lento
- Basicamente o a classe `FileReader` junta as funcionalidades das classes **`FileInputStream`** + **`InputStreamReader`**

**Pontos a se atentar:**

- Antes do `Java 11`, o `FileReader` não permitia especificar o *encoding* *(conjunto de regras que define como caracteres são representados em um formato de dados específico)*, então em aplicações legadas, pode gerar **problemas com caracteres especiais** (`ç`, `á`, `ã`, etc) **caso trabalhe com arquivos com *encoding* diferente**.
- Para aplicações profissionais é recomendado utilizar **`FileInputStream`** + **`InputStreamReader`** para ter maior flexibilidade

**Principais métodos utilizados:**

| Método | Descrição | Exemplo |
| --- | --- | --- |
| `read()` | Lê um caractere (retorna o `int` que representa aquele caractere) | `fr.read()` |
| `read(char[] cbuf)` | Lê blocos de caracteres para um array | `fr.read(buffer)` |
| `close()` | Fecha o *stream* | `fr.close()` |

## 🔹`FileInputStream` e `InputStreamReader`:

### `FileInputStream`

→ A classe `FileInputStream` é usada para **ler dados brutos (bytes) de um arquivo, sem interpretar nada:**

- Trabalha com bytes (*dados binários*). Cada leitura retorna um valor entre 0 e 255 (1 byte)
- É útil para qualquer tipo de arquivo: PDF, imagem, MP3, CSV, texto, etc.
- **Não converte para caracteres nem interpreta nada.**

```java
FileInputStream fileInputStream = new FileInputStream("ArquivoTeste.csv");
```

### `InputStreamReader`

→ Classe usada para converter **bytes para caracteres**.

- Envolve um `InputStream` (`FileInputStream`, `SocketInputStream`, etc.).
- Converte os **bytes** lidos em **caracteres**, aplicando um **Charset (**ex: UTF-8, ISO-8859-1**)**.

```java
InputStreamReader inputStreamReader = new InputStreamReader(fileInputStream, StandardCharsets.UTF_8);
```

## 🔹 `BufferedReader`:

→ Classe que funciona como um “*buffer*” melhorando a leitura de um `Reader`.

**Características:**

- Sempre usado “por cima” de outro `Reader` (`FileReader`, `InputStreamReader`, etc.).
- O `BufferedReader` permite a leitura de **blocos de dados na memória**, reduzindo I/O. Sem `BufferedReader`, o leitor faria uma chamada ao disco a cada caractere.
- Performance muito melhor
- Adiciona métodos úteis como `readLine()` que *não existem* nos `Reader`.

```java
BufferedReader reader = new BufferedReader(inputStreamReader);
```

**Principais métodos utilizados:**

| Método | Descrição | Exemplo |
| --- | --- | --- |
| `read()` | Lê um caractere | `br.read()` |
| `readLine()` | Lê uma linha inteira (até "\n") | `br.readLine()` |
| `ready()` | Verifica se o stream está pronto para leitura | `br.ready()` |
| `skip(long n)` | Pula `n` caracteres | `br.skip(5)` |
| `close()` | Fecha o stream | `br.close()` |

## 🔹 Método `split()`:

→ Método da classe `String` que **divide uma string em um array de substrings** usando um **delimitador (`,`, `;`, `“ ”`)** fornecido como uma **expressão regular**

**Uso típico em arquivos CSV:**

- Considerando que os registros em arquivos CSV utilizam delimitadores (`,`, `;`, `|`) para separar as colunas, o método `split()` é de **suma importância para a extração e manipulação** desses dados.

Variações do método `split()`:

| Método | Descrição | Exemplo |
| --- | --- | --- |
| **`split(String regex)`** | Divide a string de acordo com a expressão regular (*regex*) fornecida | `"12,32,43".split(",")` → `["12", "32", "43”]` |
| **`split(String regex, int limit)`** | Divide a string usando a expressão regular, mas o `limit` controla o número máximo de elementos no array resultante. | `“Pedro Cardoso da Silva”.split(” ”, 3)` → `[”Pedro”, “Cardoso”, “da Silva”]` |

## 🔹 `Charset`:

→ `Charset` representa um **conjunto de caracteres e regras de codificação** (*encoding*). Ela define **como transformar bytes ↔ caracteres**.

**Como funciona?**

Quando um arquivo é lido, ele vem como bytes. O `Charset` diz ao Java **como interpretar esses bytes** (ex: `UTF-8`, `ISO-8859-1`, `Windows-1252`).

**Por que isso importa ao ler CSV?**

→ Porque **se você usar o `Charset` errado**, pode aparecer:

- � caracteres estranhos
- acentos quebrados (ex: “Maçã” vira “MaÃ§Ã£”)
- erro de *parse* em colunas

👉 Exemplo: Se o CSV foi salvo em **`ISO-8859-1`**, mas você ler como `UTF-8`, os acentos virão errados.

```java
StandardCharsets.UTF_8
StandardCharsets.ISO_8859_1
StandardCharsets.UTF_16
```

## 🔹 `try-with-resources`

→ O `try-with-resources` é um **recurso sintático do Java** que serve para garantir o **fechamento automático de recursos que implementam a interface** `AutoCloseable` (como conexões de arquivos, bancos de dados ou redes).

**Porque utilizar:**
• Evita vazamento de recursos, quando o programador esquece de fechá-lo ou não trata corretamente uma situação excepcional.

**❌Errado (não recomendado):**

```java
BufferedReader br = new BufferedReader(...);
br.close();
```

**✅Certo (boa prática):**

```java
try (BufferedReader br = new BufferedReader(...)) {
    // leitura
}
```

## Ordem de montagem do Leitor:

`File` → `FileInputStream` → `InputStreamReader (com Charset)` → `BufferedReader`

## Exemplo prático de Leitor de arquivos:

```java
public class LeitorCSV {

    public static void main(String[] args) {
        // Caminho do arquivo
        File arquivo = new File("usuarios.csv");

        // Leitura com encoding seguro + fechamento automático
        try (BufferedReader reader = new BufferedReader(
                new InputStreamReader(new FileInputStream(arquivo), StandardCharsets.UTF_8))) {

            String linha;
            boolean primeiraLinha = true;

            while ((linha = reader.readLine()) != null) {
                // Ignora o cabeçalho
                if (primeiraLinha) {
                    primeiraLinha = false;
                    continue;
                }

                // Divide as colunas (formato CSV simples)
                String[] colunas = linha.split(",");

                // Extração dos dados (trim remove espaços extras)
                String id = colunas[0].trim();
                String nome = colunas[1].trim();
                String email = colunas[2].trim();
                String idade = colunas[3].trim();
                String cidade = colunas[4].trim();
                String ativo = colunas[5].trim();

                // Exibição 
                System.out.printf("Usuário: %s (%s) - %s anos, %s - Ativo: %s%n",
                        nome, email, idade, cidade, ativo);
            }

        } catch (IOException e) {
            System.err.println("Erro ao ler o arquivo: " + e.getMessage());
        }
    }
}

```
