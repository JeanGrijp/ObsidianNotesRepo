---
tags:
  - Tecnologia
  - back-end
  - GoLang
---
Goroutines são uma funcionalidade fundamental em [[Go]] que permite executar funções ou métodos de forma concorrente, ou seja, em paralelo com outras partes do programa. Elas são leves, eficientes e facilitam a escrita de programas que fazem múltiplas tarefas ao mesmo tempo.

### O que são Goroutines?

- **Concorrência Simplificada**: Em vez de trabalhar com threads pesadas do sistema operacional, Go oferece goroutines, que são muito mais leves e gerenciadas pela própria linguagem.
- **Sintaxe Simples**: Para iniciar uma goroutine, basta adicionar a palavra-chave `go` antes da chamada da função.

### Por que usar Goroutines?

- **Desempenho**: Permitem que seu programa utilize melhor os recursos do processador, executando tarefas simultaneamente.
- **Eficiência**: Como são leves, você pode ter milhares de goroutines rodando sem sobrecarregar o sistema.
- **Facilidade**: Simplificam o código concorrente, tornando-o mais fácil de ler e manter.

### Exemplo 1: Executando uma Goroutine Simples

```go
package main

import (
    "fmt"
    "time"
)

func saudacao(nome string) {
    for i := 0; i < 5; i++ {
        fmt.Println("Olá,", nome)
        time.Sleep(time.Millisecond * 500)
    }
}

func main() {
    go saudacao("Maria") // Executa como goroutine
    saudacao("João")     // Executa normalmente
}
```

**O que acontece nesse código?**

- `go saudacao("Maria")`: Inicia a função `saudacao` como uma goroutine. Ela começa a executar em paralelo.
- `saudacao("João")`: Executa a função `saudacao` de forma síncrona. O programa espera essa função terminar antes de prosseguir.
- Como resultado, as mensagens de "Olá, Maria" e "Olá, João" são intercaladas.

### Exemplo 2: Sincronizando Goroutines com WaitGroup

Para garantir que todas as goroutines terminem antes do programa encerrar, podemos usar `sync.WaitGroup`.

```go
package main

import (
    "fmt"
    "sync"
)

func contar(id int, wg *sync.WaitGroup) {
    defer wg.Done() // Indica que a goroutine terminou
    for i := 1; i <= 5; i++ {
        fmt.Printf("Goroutine %d: %d\n", id, i)
    }
}

func main() {
    var wg sync.WaitGroup

    wg.Add(2) // Dizemos que temos 2 goroutines

    go contar(1, &wg)
    go contar(2, &wg)

    wg.Wait() // Espera todas as goroutines terminarem
    fmt.Println("Todas as goroutines finalizaram.")
}
```

**Explicação:**

- **sync.WaitGroup**: É usado para esperar que um conjunto de goroutines termine.
- `wg.Add(2)`: Informamos que vamos aguardar duas goroutines.
- `wg.Done()`: Cada goroutine chama isso quando termina, decrementando o contador.
- `wg.Wait()`: O `main` fica bloqueado aqui até que o contador chegue a zero.

### Exemplo 3: Comunicação entre Goroutines com Channels

[[Channels]] são utilizados para comunicação segura entre goroutines.

```go
package main

import (
    "fmt"
)

func produtor(canal chan string) {
    mensagens := []string{"Mensagem 1", "Mensagem 2", "Mensagem 3"}
    for _, msg := range mensagens {
        canal <- msg // Envia a mensagem pelo canal
    }
    close(canal) // Fecha o canal
}

func consumidor(canal chan string) {
    for mensagem := range canal {
        fmt.Println("Recebido:", mensagem)
    }
}

func main() {
    canal := make(chan string)

    go produtor(canal)
    consumidor(canal)
}
```

**Como funciona:**

- **Produtor**: Envia mensagens para o canal.
- **Consumidor**: Recebe e processa as mensagens do canal.
- **Canal**: Serve como meio de comunicação entre as goroutines.

### Quando usar Goroutines?

- **Operações Independentes**: Tarefas que podem ser executadas em paralelo sem depender uma da outra.
- **I/O Concorrente**: Quando você precisa ler ou escrever em vários recursos simultaneamente, como arquivos ou conexões de rede.
- **Melhorar a Responsividade**: Em aplicações que não podem ficar bloqueadas esperando uma tarefa longa terminar.

### Boas Práticas

- **Evite Concorrência Não Controlada**: Use mecanismos como channels ou `sync.WaitGroup` para sincronizar suas goroutines.
- **Cuidado com Condições de Corrida**: Quando múltiplas goroutines acessam a mesma variável, use mutexes ou outras formas de sincronização.
- **Feche Channels**: Sempre feche os channels quando não forem mais usados para evitar deadlocks.

### Resumo

Goroutines são uma ferramenta poderosa em Go para implementar concorrência de forma simples e eficiente. Elas permitem que você execute múltiplas tarefas ao mesmo tempo, melhorando o desempenho e a responsividade do seu programa.

**Experimente**: Tente modificar os exemplos acima ou criar os seus próprios para entender melhor como as goroutines funcionam!