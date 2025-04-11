---
tags:
  - Tecnologia
  - GoLang
  - back-end
---
Em Go, `defer` é uma palavra-chave usada para adiar a execução de uma função até que a função que a contém seja encerrada. As instruções `defer` são frequentemente utilizadas para garantir que recursos sejam liberados, como fechar arquivos ou desbloquear mutexes, mesmo que ocorra um erro ou um retorno antecipado.

Aqui está um exemplo simples:

```go
package main

import "fmt"

func main() {
    fmt.Println("Início da função")

    defer fmt.Println("Defer executado no final da função")

    fmt.Println("Final da função")
}
```

A saída será:
```
Início da função
Final da função
Defer executado no final da função
```

No exemplo, a linha com `defer` é executada **depois** que todas as outras instruções na função `main` foram concluídas, mesmo que tenha sido chamada antes.