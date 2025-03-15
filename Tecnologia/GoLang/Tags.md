---
tags:
  - Tecnologia
  - GoLang
---
No [[Go]], ao trabalhar com `JSON` e `structs`, você pode usar **`tags`** para personalizar a representação das propriedades da [struct](Structs.md) em JSON. Abaixo, explico as formas mais comuns de representar JSON e as possibilidades que você tem ao configurar `structs`:

---

### 1. **Nome Simples da Propriedade**

Por padrão, os campos de uma struct serão mapeados para JSON com o mesmo nome **exato do campo em Go**, mas isso é case-sensitive. Para personalizar o nome da propriedade JSON, você usa **tags** no formato:

```go
`json:"nome_desejado"`
```

**Exemplo:**

```go
type Person struct {
    Name string `json:"name"` // O campo Name será "name" no JSON
    Age  int    `json:"age"`  // O campo Age será "age" no JSON
}
```

---

### 2. **Ignorar Propriedades**

Se você não quer que um campo específico apareça no JSON, pode usar um traço `-` na tag:

**Exemplo:**

```go
type Person struct {
    Name  string `json:"name"`
    Age   int    `json:"age"`
    Token string `json:"-"` // Este campo será ignorado no JSON
}
```

JSON resultante:

```json
{
  "name": "Jean",
  "age": 30
}
```

---

### 3. **Campos Opcionais**

Você pode omitir campos que tenham valores padrão (zero value) no JSON usando a tag `omitempty`:

**Exemplo:**

```go
type Person struct {
    Name  string `json:"name"`
    Age   int    `json:"age,omitempty"` // Omitido se Age for 0
    Email string `json:"email,omitempty"`
}
```

Com valores padrão:

```json
{
  "name": "Jean"
}
```

Com valores preenchidos:

```json
{
  "name": "Jean",
  "age": 30,
  "email": "jean@example.com"
}
```

---

### 4. **Aninhamento de Propriedades**

Se uma struct contém outra struct como campo, os campos internos também podem ser serializados no JSON.

**Exemplo:**

```go
type Address struct {
    Street string `json:"street"`
    City   string `json:"city"`
}

type Person struct {
    Name    string  `json:"name"`
    Address Address `json:"address"`
}
```

JSON resultante:

```json
{
  "name": "Jean",
  "address": {
    "street": "123 Main St",
    "city": "Recife"
  }
}
```

---

### 5. **Representar como um Objeto Genérico**

Se você não sabe o formato exato do JSON ou ele pode variar, você pode usar um **mapa genérico**:

**Exemplo:**

```go
data := map[string]interface{}{
    "name": "Jean",
    "age":  30,
    "hobbies": []string{"coding", "investing"},
}
```

JSON resultante:

```json
{
  "name": "Jean",
  "age": 30,
  "hobbies": ["coding", "investing"]
}
```

---

### 6. **Tags Múltiplas**

Você pode usar múltiplas tags em um campo, como `json` e `xml`, para suportar diferentes formatos de serialização:

**Exemplo:**

```go
type Person struct {
    Name string `json:"name" xml:"name"`
    Age  int    `json:"age,omitempty" xml:"age"`
}
```

---

### 7. **Tratar JSON como String**

Se o JSON for armazenado como string em um campo, use:

**Exemplo:**

```go
type Person struct {
    RawData json.RawMessage `json:"raw_data"` // Armazena JSON cru
}
```

---

### Resumo das Principais Tags:

|Tag|Descrição|
|---|---|
|`json:"name"`|Define o nome da propriedade no JSON.|
|`json:"-"`|Ignora o campo no JSON.|
|`json:",omitempty"`|Omite o campo se ele tiver o valor zero (como `""`, `0`, `nil` ou `false`).|
|`json:",string"`|Serializa/deserializa o campo como string (mesmo que seja um número ou outro tipo).|
|`json:"name,omitempty"`|Combina renomear e omitir valor padrão.|

### 8. **Serializar/Deserializar como String (`json:",string"`)**

- Essa tag força um campo a ser serializado ou deserializado como uma string, mesmo que o tipo real do campo seja diferente (como um número ou booleano).
- Útil para APIs que esperam números ou valores booleanos como strings.

**Exemplo:**

```go
type Person struct {
    Age int `json:"age,string"` // Serializa/deserializa o Age como string
}
```

JSON resultante:

```json
{
  "age": "30"
}
```

Ao deserializar, o Go converte `"30"` de volta para `int`.

---

### 9. **Tratar Campos JSON Dinâmicos com `json.RawMessage`**

- O campo `json.RawMessage` permite armazenar dados JSON "crus" (sem deserializar automaticamente) e processá-los posteriormente.

**Exemplo:**

```go
type Person struct {
    Name    string          `json:"name"`
    Details json.RawMessage `json:"details"` // Armazena JSON cru
}

func main() {
    data := `{"name": "Jean", "details": {"age": 30, "city": "Recife"}}`
    var p Person
    json.Unmarshal([]byte(data), &p)
    fmt.Println(string(p.Details)) // Output: {"age":30,"city":"Recife"}
}
```

---

### 10. **Campos Embutidos (Anonymous Fields)**

- Se você usa uma struct anônima, os campos da struct embutida são "promovidos" e aparecem diretamente no JSON.

**Exemplo:**

```go
type Address struct {
    City string `json:"city"`
}

type Person struct {
    Name string  `json:"name"`
    Address
}
```

JSON resultante:

```json
{
  "name": "Jean",
  "city": "Recife"
}
```

---

### 11. **Renomear ou Configurar Structs Inteiras com `omitempty`**

- Em structs anônimas ou embutidas, você pode aplicar `omitempty` na struct inteira para ignorar todos os campos que tiverem valores padrão.

**Exemplo:**

```go
type Address struct {
    City  string `json:"city,omitempty"`
    State string `json:"state,omitempty"`
}

type Person struct {
    Name    string  `json:"name"`
    Address Address `json:"address,omitempty"` // Ignora Address se todos os campos forem vazios
}
```

---

### 12. **Campos Privados (não exportados)**

- Campos privados (com letras minúsculas no nome) **não são serializáveis em JSON**, mesmo sem tags. Apenas campos exportados (letras maiúsculas) são processados pelo pacote `encoding/json`.

**Exemplo:**

```go
type Person struct {
    Name string // Exportado
    age  int    // Não exportado (não aparecerá no JSON)
}
```

JSON resultante:

```json
{
  "Name": "Jean"
}
```

---

### 13. **Usar Tipos Customizados**

- Você pode criar tipos customizados (por exemplo, baseado em `int` ou `string`) e implementar a interface `json.Marshaler` ou `json.Unmarshaler` para controlar como eles são serializados/deserializados.

**Exemplo:**

```go
type MyDate string

func (d MyDate) MarshalJSON() ([]byte, error) {
    return json.Marshal(string(d) + "T00:00:00Z")
}

func (d *MyDate) UnmarshalJSON(data []byte) error {
    var date string
    if err := json.Unmarshal(data, &date); err != nil {
        return err
    }
    *d = MyDate(date[:10]) // Remove o horário
    return nil
}

type Event struct {
    Date MyDate `json:"date"`
}
```

---

### 14. **Tags de Struct Customizadas**

- O pacote `encoding/json` permite combinar tags de outros pacotes, desde que o pacote faça uso do mesmo formato de tag. Por exemplo, você pode usar tags personalizadas junto com `json`.

**Exemplo:**

```go
type Person struct {
    Name string `json:"name" validate:"required"` // Combina JSON com validação
}
```

---

### 15. **Manipulação de Arrays e Mapas**

- Você pode trabalhar diretamente com slices e mapas para lidar com JSON mais dinâmicos.

**Exemplo:**

```go
type Data struct {
    Names []string          `json:"names"`
    Info  map[string]string `json:"info"`
}
```

JSON:

```json
{
  "names": ["Jean", "Maria"],
  "info": {"age": "30", "city": "Recife"}
}
```

---

### 16. **Omitempty em Campos Compostos**

- `omitempty` em campos como slices, mapas ou structs ignora esses campos no JSON se eles estiverem vazios.

**Exemplo:**

```go
type Person struct {
    Hobbies []string `json:"hobbies,omitempty"`
}
```

