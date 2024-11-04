---
tags:
  - Tecnologia
  - back-end
  - GoLang
---
Em Go, arrays e slices são estruturas fundamentais para armazenar coleções de elementos do mesmo tipo. Embora Go não possua métodos embutidos para manipulação de arrays e slices, a linguagem oferece funções e operações que permitem realizar diversas manipulações. Abaixo, destacamos as operações e funções mais comuns:

### 1. Declaração e Inicialização

- **Arrays**: Possuem tamanho fixo definido na declaração.

  ```go
  var arr [5]int
  arr := [5]int{1, 2, 3, 4, 5}
  ```

- **Slices**: Têm tamanho dinâmico e são mais flexíveis.

  ```go
  var s []int
  s := []int{1, 2, 3, 4, 5}
  s := make([]int, 5) // Cria um slice com capacidade e tamanho 5
  ```

### 2. Acesso e Modificação de Elementos

Os elementos podem ser acessados e modificados usando índices:

```go
element := s[2]    // Acessa o terceiro elemento
s[2] = 10          // Modifica o terceiro elemento para 10
```

### 3. Obtenção de Comprimento e Capacidade

Utilize as funções integradas `len` e `cap`:

```go
length := len(s)   // Comprimento do slice
capacity := cap(s) // Capacidade do slice
```

### 4. Adição de Elementos

A função `append` permite adicionar elementos a um slice:

```go
s = append(s, 6)               // Adiciona um único elemento
s = append(s, 7, 8, 9)         // Adiciona múltiplos elementos
s = append(s, anotherSlice...) // Adiciona todos os elementos de outro slice
```

**Nota**: Ao adicionar elementos que excedem a capacidade atual do slice, o Go aloca um novo array subjacente com capacidade aumentada.

### 5. Copiando Slices

A função `copy` é utilizada para copiar elementos de um slice para outro:

```go
src := []int{1, 2, 3}
dst := make([]int, len(src))
copy(dst, src) // Copia elementos de src para dst
```

### 6. Remoção de Elementos

Para remover elementos, é necessário manipular os slices manualmente:

- **Remover um elemento no índice `i`:**

  ```go
  s = append(s[:i], s[i+1:]...)
  ```

- **Remover os primeiros `n` elementos:**

  ```go
  s = s[n:]
  ```

- **Remover os últimos `n` elementos:**

  ```go
  s = s[:len(s)-n]
  ```

### 7. Iteração sobre Arrays e Slices

Utilize o loop `for` com `range` para iterar:

```go
for index, value := range s {
    fmt.Println(index, value)
}
```

Se o índice não for necessário:

```go
for _, value := range s {
    fmt.Println(value)
}
```

### 8. Ordenação

Para ordenar slices, utilize o pacote `sort`:

```go
import "sort"

sort.Ints(s)    // Ordena slice de inteiros
sort.Strings(s) // Ordena slice de strings
```

Para tipos personalizados, é necessário implementar a interface `sort.Interface`.

### 9. Busca

Para buscar elementos, é necessário implementar manualmente ou utilizar pacotes externos, pois Go não possui funções embutidas para busca em slices.

### 10. Conversão entre Arrays e Slices

Arrays podem ser convertidos em slices:

```go
arr := [5]int{1, 2, 3, 4, 5}
s := arr[:] // Converte array em slice
```
