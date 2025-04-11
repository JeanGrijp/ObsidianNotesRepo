---
tags:
  - Tecnologia
  - back-end
  - GoLang
---
Ponteiros em Go são uma maneira de acessar e manipular o valor de uma variável diretamente na memória. Eles são úteis quando você quer passar referências de dados para funções ou evitar cópias desnecessárias de valores grandes.

### Conceitos básicos

Em Go, cada variável possui um endereço de memória onde seu valor é armazenado. Um ponteiro é uma variável que "aponta" para esse endereço, ou seja, ele guarda o local onde o valor de outra variável está armazenado.

Para trabalhar com ponteiros, temos dois operadores principais:

1. **&** (operador de endereço): Obtém o endereço de memória de uma variável.
2. **\*** (operador de desreferência): Acessa o valor na memória para a qual o ponteiro aponta.

### Exemplo 1: Declarando e usando ponteiros

```go
package main

import "fmt"

func main() {
    var x int = 10       // declaramos uma variável "x" com valor 10
    var p *int = &x      // "p" é um ponteiro que guarda o endereço de "x"

    fmt.Println("Valor de x:", x)       // imprime 10
    fmt.Println("Endereço de x:", &x)   // imprime o endereço de "x"
    fmt.Println("Valor de p:", p)       // imprime o endereço de "x" (guardado em "p")
    fmt.Println("Valor apontado por p:", *p) // desreferenciando "p", imprime 10
}
```

### Exemplo 2: Modificando valores usando ponteiros

Usando ponteiros, você pode alterar o valor de uma variável em uma função sem retornar nada.

```go
package main

import "fmt"

// função que altera o valor de um número
func alterarNumero(n *int) {
    *n = *n + 10  // desreferencia o ponteiro "n" e incrementa 10
}

func main() {
    numero := 20
    fmt.Println("Antes:", numero) // 20

    alterarNumero(&numero)        // passa o endereço de "numero"
    fmt.Println("Depois:", numero) // 30
}
```

Neste exemplo, a função `alterarNumero` recebe um ponteiro para `numero`. Isso permite que a função altere o valor da variável original em vez de trabalhar com uma cópia.

### Vantagens dos ponteiros em Go:

- **Economia de memória**: Ao passar um ponteiro para uma função, você evita a cópia de valores grandes, o que é mais eficiente.
- **Modificação direta**: Permite alterar o valor da variável original dentro de uma função.

Ponteiros em Go são especialmente úteis ao trabalhar com grandes estruturas de dados ou quando você deseja modificar valores diretamente sem retornar valores.