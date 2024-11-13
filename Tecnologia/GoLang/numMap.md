---
tags:
  - Tecnologia
  - GoLang
---
Em Golang, um **map** é uma estrutura de dados que associa chaves a valores, semelhante a um dicionário ou tabela hash em outras linguagens de programação. O termo **`numMap`** provavelmente refere-se a um mapa que utiliza números, seja como chaves, valores ou ambos. Vou explicar de forma simples o que é um mapa em Go e como você pode usar um `numMap`.

### O que é um map em Go?

Um map em Go é uma coleção não ordenada de pares chave-valor, onde cada chave é única dentro do mapa. A sintaxe básica para declarar um mapa é:

```go
map[KeyType]ValueType
```

- **KeyType**: o tipo das chaves (ex.: `int`, `string`).
- **ValueType**: o tipo dos valores associados às chaves.

### Como criar e usar um numMap?

Vamos criar um exemplo de `numMap` que mapeia números inteiros para strings (por exemplo, códigos de erro para mensagens).

```go
package main

import "fmt"

func main() {
    // Cria um mapa que associa int a string
    numMap := make(map[int]string)

    // Adiciona pares chave-valor ao mapa
    numMap[200] = "OK"
    numMap[404] = "Not Found"
    numMap[500] = "Internal Server Error"

    // Acessa um valor através de sua chave
    fmt.Println("Código 200 significa:", numMap[200])

    // Verifica se uma chave existe no mapa
    if msg, exists := numMap[301]; exists {
        fmt.Println("Código 301 significa:", msg)
    } else {
        fmt.Println("Código 301 não encontrado.")
    }

    // Itera sobre os pares chave-valor do mapa
    for code, message := range numMap {
        fmt.Printf("Código %d: %s\n", code, message)
    }

    // Remove um par chave-valor do mapa
    delete(numMap, 500)

    // Exibe o mapa após a remoção
    fmt.Println("Após remover o código 500:", numMap)
}
```

#### Explicação do código:

- **Criação do mapa**:
  ```go
  numMap := make(map[int]string)
  ```
  Utilizamos a função `make` para inicializar um mapa vazio onde as chaves são do tipo `int` e os valores do tipo `string`.

- **Inserção de valores**:
  ```go
  numMap[200] = "OK"
  ```
  Atribuímos o valor `"OK"` à chave `200`.

- **Acesso a valores**:
  ```go
  fmt.Println("Código 200 significa:", numMap[200])
  ```
  Recuperamos o valor associado à chave `200`.

- **Verificação de existência**:
  ```go
  if msg, exists := numMap[301]; exists {
      // Chave existe
  } else {
      // Chave não existe
  }
  ```
  Utilizamos a sintaxe de atribuição dupla para verificar se a chave `301` existe no mapa.

- **Iteração sobre o mapa**:
  ```go
  for code, message := range numMap {
      // code é a chave, message é o valor
  }
  ```
  O loop `for range` nos permite percorrer todos os pares chave-valor.

- **Remoção de um item**:
  ```go
  delete(numMap, 500)
  ```
  A função `delete` remove o par chave-valor associado à chave `500`.

### Dicas importantes:

- **Tipos de chaves**: As chaves em um mapa devem ser de um tipo que seja comparável (tipos que podem ser usados com os operadores `==` ou `!=`), como `int`, `string`, `bool`, etc.

- **Inicialização**: Sempre inicialize um mapa antes de usá-lo, seja com `make` ou atribuindo um literal de mapa.

  ```go
  // Usando make
  numMap := make(map[int]string)

  // Usando um literal de mapa
  numMap := map[int]string{
      200: "OK",
      404: "Not Found",
  }
  ```

- **Valores zero**: Se você acessar uma chave que não existe no mapa, o Go retornará o valor zero do tipo de valor do mapa. Por exemplo, para um `map[int]string`, o valor zero é `""` (string vazia).

### Quando usar mapas?

- Quando você precisa de uma associação rápida de chaves a valores.
- Para implementar tabelas de consulta ou cache.
- Quando a ordem dos elementos não é importante.

### Exemplo prático: Contagem de ocorrências

Vamos supor que você queira contar quantas vezes cada número aparece em um slice de inteiros.

```go
func main() {
    numeros := []int{1, 2, 2, 3, 3, 3, 4, 4, 4, 4}
    contagem := make(map[int]int)

    for _, num := range numeros {
        contagem[num]++
    }

    fmt.Println("Contagem de ocorrências:", contagem)
}
```

#### Explicação:

- Criamos um mapa `contagem` onde a chave é o número e o valor é o número de ocorrências.
- Iteramos sobre o slice `numeros` e incrementamos o contador no mapa para cada número encontrado.

### Conclusão

O `numMap` é simplesmente um exemplo de mapa em Go que associa chaves numéricas a valores (ou vice-versa). Mapas são uma parte fundamental da linguagem Go e são extremamente úteis para diversos tipos de aplicações que requerem associações rápidas e eficientes entre chaves e valores.

**Referências adicionais:**

- [A Tour of Go - Maps](https://tour.golang.org/moretypes/19)
- [Effective Go - Maps](https://golang.org/doc/effective_go.html#maps)

Espero que esta explicação tenha ajudado você a entender o que é um `numMap` em Golang e como usá-lo!