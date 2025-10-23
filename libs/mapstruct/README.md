# MapStruct

O MapStruct é um _code generator_ (gerador de código) que cria automaticamente implementações de _mappers_ (interfaces responsáveis por converter objetos).
Ou seja, você define **o que** deve ser mapeado, e o MapStruct gera o **como**.


👉 **Ele é muito usado para:**
- Converter entidades JPA em DTOs.
- Converter DTOs em entidades (para salvar ou atualizar dados).
- Converter objetos de requisição (Request) em objetos de domínio.
- Evitar `ModelMapper`, que usa reflection (mais lento e menos seguro).

🔗[Dependência Maven do MapStruct](https://mvnrepository.com/artifact/org.mapstruct/mapstruct)




# Anottations:

## `@Mapping`

### 1️⃣ O que é a anotação `@Mapping`

A anotação `@Mapping` serve para **configurar manualmente como um campo de origem (por exemplo, de um Entity ou DTO) deve ser mapeado para um campo de destino**.
Ela é usada dentro da interface do Mapper, acima do método que faz a conversão.

Exemplo prático:
```java
@Mapper(componentModel = "spring")
public interface DisdispositivoMapper {

    //Entity -> DispositivoresponseDTO
    @Mapping()
    DispositivoResponseDTO toResponseDTO(Dispositivo dispositivo);

}
```

### 2️⃣ Para que servem `source` e `target`

Esses dois atributos dizem ao MapStruct de **onde vem o valor e para onde ele vai**.

| Atributo	| Significado	| Exemplo |  
|-----------|-------------|---------|
| `source`	| Nome do campo no objeto de origem (source object)	| `"usuario.id"` |   
| `target`	| Nome do campo no objeto de destino (target object) |	`"usuarioId"` |   

👉 Em resumo:

`source` → origem dos dados  
`target` → destino dos dados

**Exemplo prático**

Suponha que você tenha isso:

Entity
```java
public class Dispositivo {
    private Long id;
    private String modelo;
    private Usuario usuario;
}
```
DTO
```java
public class DispositivoResponseDTO {
    private Long id;
    private String modelo;
    private Long usuarioId;
}
```

👉 Aqui, o `usuarioId` não existe diretamente em `Dispositivo`, mas sim dentro de `usuario`.

Então o MapStruct não conseguiria inferir sozinho — e é aí que entra o `@Mapping`:
```java
@Mapper(componentModel = "spring")
public interface DispositivoMapper {

    @Mapping(source = "usuario.id", target = "usuarioId")
    DispositivoResponseDTO toResponseDTO(Dispositivo dispositivo);
}
```

👉 Nesse caso:

`source` = `"usuario.id"` → vem do objeto `Dispositivo` → acessa `getUsuario().getId()`

`target` = `"usuarioId"` → vai pro campo `usuarioId` no `DispositivoResponseDTO`


### 3️⃣ Quando devo usar `@Mapping`

Use quando:

✅ Os nomes dos campos são diferentes  
✅ Há objetos aninhados (como `usuario.id` → `usuarioId`)  
✅ Quer ignorar campos (`@Mapping(target = "id", ignore = true)`)  
✅ Precisa de conversão customizada (`date` → `string`, `enum` → `int`, etc.)  
