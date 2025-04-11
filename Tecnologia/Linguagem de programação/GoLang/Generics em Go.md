---
tags:
  - Tecnologia
  - back-end
  - GoLang
---
Generics permitem que você crie funções e tipos (como estruturas) que podem trabalhar com qualquer tipo de dado. Em vez de escrever versões separadas da mesma função para cada tipo de dado (por exemplo, `int`, `float64`, `string`), você escreve uma única versão genérica que funciona com todos eles.

### Por que usar Generics?

Imagine que você quer criar uma função que retorna o maior valor entre dois números. Sem generics, você precisaria escrever uma função para cada tipo:

```go
func MaxInt(a, b int) int {
    if a > b {
        return a
    }
    return b
}

func MaxFloat(a, b float64) float64 {
    if a > b {
        return a
    }
    return b
}
```

Com generics, você escreve apenas uma função:

```go
func Max[T comparable](a, b T) T {
    if a > b {
        return a
    }
    return b
}
```

### Como funciona?

- **`[T comparable]`**: Aqui, `T` é um parâmetro de tipo genérico. `comparable` é uma restrição que indica que os valores podem ser comparados usando `>` e `<`.
- **Parâmetros Genéricos**: Você define uma função ou tipo usando parâmetros genéricos dentro de colchetes `[]`.
- **Uso**: Quando você chama a função, o Go automaticamente infere o tipo com base nos argumentos que você passa.

### Exemplo Prático 1

```go
package main

import "fmt"

func FirstElement[T any](slice []T) T {
    return slice[0]
}

func main() {
    numbers := []int{1, 2, 3}
    words := []string{"Olá", "Mundo"}

    fmt.Println(FirstElement(numbers)) // Saída: 1
    fmt.Println(FirstElement(words))   // Saída: Olá
}
```

- **`[T any]`**: O `any` indica que `T` pode ser de qualquer tipo.
- **Reutilização**: A mesma função `FirstElement` funciona tanto para slices de `int` quanto de `string`.

### Vantagens dos Generics

- **Código Mais Limpo**: Menos repetição de código.
- **Flexibilidade**: Funções e tipos mais flexíveis que podem trabalhar com diversos tipos de dados.
- **Manutenção**: Facilita a manutenção e atualização do código.

### 2. Função Genérica para Filtrar Slices

Uma função genérica pode filtrar elementos de um slice com base em um critério definido:

```go
package main

import "fmt"

// Filter retorna um novo slice contendo os elementos que satisfazem o predicado.
func Filter[T any](s []T, predicate func(T) bool) []T {
    var result []T
    for _, v := range s {
        if predicate(v) {
            result = append(result, v)
        }
    }
    return result
}

func main() {
    numbers := []int{1, 2, 3, 4, 5}
    evens := Filter(numbers, func(n int) bool { return n%2 == 0 })
    fmt.Println(evens) // Saída: [2 4]
}
```

Aqui, `Filter` é uma função genérica que aceita um slice de qualquer tipo e um predicado que determina quais elementos devem ser incluídos no resultado.

### 3. Tipo Genérico para Pilha (Stack)

Podemos definir uma estrutura de dados genérica, como uma pilha (stack), que funciona com qualquer tipo de dado:

```go
package main

import "fmt"

// Stack representa uma pilha genérica.
type Stack[T any] struct {
    elements []T
}

// Push adiciona um elemento à pilha.
func (s *Stack[T]) Push(element T) {
    s.elements = append(s.elements, element)
}

// Pop remove e retorna o elemento do topo da pilha.
func (s *Stack[T]) Pop() (T, bool) {
    if len(s.elements) == 0 {
        var zero T
        return zero, false
    }
    index := len(s.elements) - 1
    element := s.elements[index]
    s.elements = s.elements[:index]
    return element, true
}

func main() {
    var intStack Stack[int]
    intStack.Push(10)
    intStack.Push(20)
    fmt.Println(intStack.Pop()) // Saída: 20, true
    fmt.Println(intStack.Pop()) // Saída: 10, true
    fmt.Println(intStack.Pop()) // Saída: 0, false
}
```

Neste exemplo, `Stack` é uma estrutura genérica que pode armazenar elementos de qualquer tipo, oferecendo métodos para adicionar (`Push`) e remover (`Pop`) elementos.

### 4. Função Genérica para Mapear Slices

Uma função genérica pode aplicar uma transformação a cada elemento de um slice, retornando um novo slice com os resultados:

```go
package main

import "fmt"

// Map aplica a função f a cada elemento do slice s e retorna um novo slice com os resultados.
func Map[T any, U any](s []T, f func(T) U) []U {
    result := make([]U, len(s))
    for i, v := range s {
        result[i] = f(v)
    }
    return result
}

func main() {
    numbers := []int{1, 2, 3}
    squares := Map(numbers, func(n int) int { return n * n })
    fmt.Println(squares) // Saída: [1 4 9]
}
```

A função `Map` é genérica em dois tipos, `T` e `U`, permitindo transformar um slice de elementos de tipo `T` em um slice de elementos de tipo `U`.

Esses exemplos ilustram como os genéricos em Go permitem a criação de código mais flexível e reutilizável, reduzindo a necessidade de duplicação e facilitando a manutenção.