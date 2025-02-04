---
tags:
  - Tecnologia
  - back-end
  - GoLang
---
Em Go, a conversão de tipos é feita de maneira explícita, e você precisa especificar o tipo para o qual deseja converter. Aqui estão alguns exemplos comuns de como fazer conversões de tipos em Go:

### 1. **Conversão de Tipos Numéricos**

Para converter de um tipo numérico para outro (como de `int` para `float64` ou vice-versa), você pode fazer o seguinte:

```go
var a int = 10
var b float64 = float64(a) // converte `a` para `float64`
var c int = int(b)         // converte `b` de volta para `int`
```

### 2. **Conversão de Inteiro para String e Vice-Versa**

Para converter um `int` para uma `string`, você pode usar a função `strconv.Itoa`. E para converter uma `string` para `int`, use `strconv.Atoi`:

```go
import "strconv"

var a int = 42
var b string = strconv.Itoa(a) // converte `int` para `string`

var str string = "123"
var c, err = strconv.Atoi(str) // converte `string` para `int`
if err != nil {
    // lidar com o erro, se necessário
}
```

### 3. **Conversão de Float para String e Vice-Versa**

Para converter um `float64` para uma `string`, use `strconv.FormatFloat`, e para converter uma `string` para `float64`, use `strconv.ParseFloat`:

```go
import "strconv"

var f float64 = 3.1415
var s string = strconv.FormatFloat(f, 'f', 6, 64) // converte `float64` para `string`

var str string = "3.1415"
var flt, err = strconv.ParseFloat(str, 64) // converte `string` para `float64`
if err != nil {
    // lidar com o erro, se necessário
}
```

### 4. **Conversão entre `string` e `[]byte`**

Para converter uma `string` para um `[]byte` (slice de bytes) e vice-versa, use a função de conversão simples:

```go
var s string = "hello"
var b []byte = []byte(s) // converte `string` para `[]byte`

var str string = string(b) // converte `[]byte` para `string`
```

### 5. **Conversão de Interfaces (`interface{}`) para Tipos Concretos**

Quando você trabalha com valores de tipo `interface{}`, é necessário fazer uma *type assertion* para convertê-los para um tipo específico:

```go
var i interface{} = "Hello, Go!"

// Type assertion para `string`
str, ok := i.(string)
if ok {
    fmt.Println("String:", str)
} else {
    fmt.Println("Não é uma string")
}
```

### 6. **Conversão de Tipos Customizados**

Se você tiver tipos personalizados, pode definir métodos de conversão ou usar conversão explícita, como:

```go
type MyInt int

var a int = 10
var b MyInt = MyInt(a) // converte `int` para `MyInt`
```
