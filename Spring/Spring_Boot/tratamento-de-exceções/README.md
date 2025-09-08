Em toda aplicação Java, cedo ou tarde, nos deparamos com a necessidade de **tratar exceções**. A primeira ideia que costuma vir à mente é recorrer aos tradicionais blocos `try-catch`, muitas vezes inseridos diretamente nos métodos dos controllers ou services:

```java
@GetMapping
@RequestMapping("/usuarios")
public class UsuarioController {

    @Autowired
    private UsuarioService usuarioService;

    @GetMapping("/{id}")
    public ResponseEntity<?> buscarPorId(@PathVariable Long id) {
        try {
            Usuario usuario = usuarioService.buscarPorId(id);
            return ResponseEntity.ok(usuario);
        } catch (UserNotFoundException e) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND).body(e.getMessage());
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Erro inesperado");
        }
    }
}
```

Apesar de funcionar, essa abordagem possui **várias desvantagens:**

- **Torna o código poluído e repetitivo**
- **Prejudica a escalabilidade**
- **Viola o princípio de responsabilidade única**
- **Dificulta a manutenção**

No contexto de aplicações Spring Boot — **principalmente APIs REST** — a **melhor prática** é adotar um tratamento de exceções de forma **centralizada e global**, por meio de uma classe separada e especializada para essa função. Isso é possível graças ao poderoso recurso fornecido pelo framework: o `@RestControllerAdvice`.

Exemplo:

Criação da Exception personalizada:

```java
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String mensagem) {
        super(mensagem);
    }
}
```

Classe Service que lançara a Exception:

```java
@Service
public class UsuarioService {
		
    @Autowired
    private UsuarioRepository usuarioRepository;

    public Usuario buscarPorId(Long id) {
        return this.usuarioRepository.findById(usuarioId)
                .orElseThrow(() -> new UsuarioNaoEncontradoException(usuarioId));
    }
}
```

Classe Controller sem tratamento manual (Jeito certo):

```java
@RestController
@RequestMapping("/usuarios")
public class UsuarioController {

    @Autowired
    private UsuarioService usuarioService;

    @GetMapping("/{id}")
    public ResponseEntity<Usuario> buscarPorId(@PathVariable Long id) {
        Usuario usuario = usuarioService.buscarPorId(id);
        return ResponseEntity.ok(usuario);
    }
}
```

Classe de tratamento global com `@RestControllerAdvice`:

```java
@RestControllerAdvice
public class GlobalHandler{

	
    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ApiError> handleUserNotFound(UserNotFoundException ex, HttpServletRequest request) {
	
        ApiError error = new ApiError(HttpStatus.NOT_FOUND, ex.getMessage(), request.getRequestURI());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
}
```

COMO FUNCIONA?

**Interceptação:**

A classe anotada com `@RestControllerAdvice` intercepta todas as exceções lançadas pelos seus controladores.

**Tratamento centralizado:**

Em vez de ter métodos de tratamento de exceção em cada controlador individualmente, você define esses métodos em sua classe `@RestControllerAdvice`.

**`@ExceptionHandler`:**

Utiliza-se a anotação `@ExceptionHandler` dentro da classe `@RestControllerAdvice` para mapear um método específico para lidar com um determinado tipo de exceção.

---

## @ControllerAdvice X @RestControllerAdvice


### **`@ControllerAdvice`**

Permite capturar exceções de **qualquer *controller***, **mas não define como a resposta será serializada (***convertido em outro formato***).** Caso **queira retornar um JSON, deve adicionar manualmente a anotação** `@ResponseBody` **em cada método** `@ExceptionHandler`**.**

Exemplo:

```java
@ControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    @ResponseBody // necessário aqui!
    public ApiError handle(UserNotFoundException ex) {
        return new ApiError(...);
    }
}
```

### **`@RestControllerAdvice`**  (`@ControllerAdvice` + `@ResponseBody`):

Mesma funcionalidade da `@ControllerAdvice`, contudo, já indica que **as respostas serão serializadas como JSON/XML**, como num `@RestController`.

Exemplo:

```java
@RestControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ApiError handle(UserNotFoundException ex) {
        return new ApiError(...); // já é serializado como JSON automaticamente
    }
}
```

⚠ **RECOMENDAÇÃO:** para criação de APIs REST, é recomendado que utilize o `@RestControllerAdvice` porque:

- Garante que todas as respostas serão **serializadas corretamente**
- Evita erro bobo por esquecer `@ResponseBody`
- Torna o código **mais limpo e profissional**

### `@ExceptionHandler(TipoDaException ex)`:

Ela permite **definir um método específico que será invocado quando uma determinada exceção ocorrer**, oferecendo a possibilidade de tratar a exceção de forma personalizada, como retornar uma resposta de erro específica.

Exemplo:

```java
@ExceptionHandler(Exception.class)
    public ResponseEntity<ApiError> handleGenericException(Exception ex, HttpServletRequest request) {
        // sua lógica
    }
}
```

---

## Boas práticas no tratamento de Exceptions:


- **Criar um DTO de erro padronizada** ➡ Todos os erros da API terão o **mesmo padrão de resposta**

```java
public class ApiError {

    private int status;
    private String error;
    private String message;
    private String path;
    private LocalDateTime timestamp;

    public ApiError(HttpStatus status, String message, String path) {
        this.status = status.value();
        this.error = status.getReasonPhrase();
        this.message = message;
        this.path = path;
        this.timestamp = LocalDateTime.*now*();
    }

    // Getters e Setters
}
```

- Nomear os métodos da classe `@ControllerAdvice` (ou `@RestControllerAdvice`) com o nome da Exception que será tratada

```java
    @ExceptionHandler(UsuarioNaoEncontradoException.class)
    public ResponseEntity<ApiError> handleUsuarioNaoEncontradoException (UsuarioNaoEncontradoException ex, HttpServletRequest request){
    
    ApiError error = new ApiError(HttpStatus.NOT_FOUND, ex.getMessage(), request.getRequestURI());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }
```

---


## Usar um Optional<T> ou disparar uma Exception?

---

Qual o melhor momento para utilizar um ou outro?

Só pensar:


> 💡
A ausência do Objeto é um erro de negócio e impede o fluxo normal da aplicação? ➡ Isso é uma **EXCEÇÃO.**

