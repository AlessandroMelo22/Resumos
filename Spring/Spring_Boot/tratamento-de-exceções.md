# Tratamento de Exceções no Spring Boot:


Para realizar o tratamento de exceções de forma “**global**” e **padronizada** (sem poluir as classes de Service ou Controller), criamos uma classe anotada com `@ControllerAdvice`(ou `@RestControllerAdvice`)

**Principais anotations:**
- `@RestControllerAdvice`
- `@ControllerAdvice`
- `ExceptionHandler(TipoDaException ex)`




**COMO FUNCIONA?**

**Interceptação:**
- A classe anotada com `@RestControllerAdvice` intercepta todas as exceções lançadas pelos seus controladores.

**Tratamento centralizado:**
- Em vez de ter métodos de tratamento de exceção em cada controlador individualmente, você define esses métodos em sua classe `@RestControllerAdvice`.

**`@ExceptionHandler`:**
- Utiliza-se a anotação `@ExceptionHandler` dentro da classe `@RestControllerAdvice` para mapear um método específico para lidar com um determinado tipo de exceção.

---  

## @ControllerAdvice X @RestControllerAdvice

### **`@ControllerAdvice`**

Permite capturar exceções de **qualquer *controller***, **mas não define como a resposta será serializada (***convertido em outro formato***).** Caso **queira retornar um JSON, deve adicionar manualmente a anotação** `@ResponseBody` **em cada método** `@ExceptionHandler`**.**

Exemplo:

```
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
```
@RestControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ApiError handle(UserNotFoundException ex) {
        return new ApiError(...); // já é serializado como JSON automaticamente
    }
}
```

⚠**RECOMENDAÇÃO:** para criação de APIs REST, é recomendado que utilize o `@RestControllerAdvice` porque:

- Garante que todas as respostas serão **serializadas corretamente**
- Evita erro bobo por esquecer `@ResponseBody`
- Torna o código **mais limpo e profissional**

  
### `@ExceptionHandler(TipoDaException ex)`:

Ela permite **definir um método específico que será invocado quando uma determinada exceção ocorrer**, oferecendo a possibilidade de tratar a exceção de forma personalizada, como retornar uma resposta de erro específica.

Exemplo:

```
 @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiError> handleGenericException(Exception ex, HttpServletRequest request) {
        // sua lógica
    }
}
```
---

## Boas práticas no tratamento de Exceptions:

- Evitar `try-catch` nas classes `Controller`➡ Utilizar uma classe apenas para realizar o tratamento de exceções evita a poluição de funcionalidades.

```
@RestController
@RequestMapping("/api/items")
public class ItemController {

    @Autowired
    private ItemService itemService;

    @GetMapping("/{id}")
    public ResponseEntity<Item> getItem(@PathVariable Long id) {
        try {
            Item item = itemService.getItemById(id);
            return ResponseEntity.ok(item);
        } catch (Exception e) {
            // Má prática: tratamento genérico de exceção
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(null);
        }
    }
}
```

- **Criar um DTO de erro padronizada** ➡ Todos os erros da API terão o **mesmo padrão de resposta**

```
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
        this.timestamp = LocalDateTime.now();
    }

    // Getters e Setters
}
```

- Nomear os métodos da classe `@ControllerAdvice` (ou `@RestControllerAdvice`) com o nome da Exception que será tratada
