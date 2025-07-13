---
tags:
  - Tecnologia
  - GoLang
---
Channels são estruturas que permitem o envio e recebimento de valores entre [[Goroutine]] de forma segura e sincronizada. Eles são parte essencial do modelo de concorrência do [[Go]], permitindo que as goroutines se comuniquem sem a necessidade de bloqueios manuais (como [[mutex]]).

O canal é criado com a função `make`:

```go
ch := make(chan int)
```

Esse canal é do tipo `chan int`, ou seja, permite a comunicação de valores inteiros.

---

## Operações básicas

### Enviar para o canal

```go
ch <- 10
```

Esse comando envia o valor `10` para o canal `ch`.

### Receber do canal

```go
valor := <-ch
```

Esse comando lê o próximo valor disponível do canal. Se não houver nenhum valor disponível e o canal não estiver fechado, a leitura bloqueia até que algum valor seja enviado.

## Bloqueio automático

Channels são **sincronizados por padrão** (também chamados de _unbuffered_), ou seja:

- Enviar (`ch <- valor`) bloqueia até que uma outra goroutine esteja pronta para receber (`<-ch`)
    
- Receber (`<-ch`) bloqueia até que haja algo no canal
    

Esse comportamento serve como uma forma natural de sincronização entre goroutines.

---

## Exemplo prático com goroutine e channel

```go
package main

import (
	"fmt"
)

func escrever(texto string, ch chan string) {
	ch <- texto
}

func main() {
	ch := make(chan string)

	go escrever("mensagem de uma goroutine", ch)

	mensagem := <-ch
	fmt.Println(mensagem)
}
```

O canal aqui funciona como uma ponte entre a goroutine que executa a função `escrever` e a função `main`.

---

## Fechamento de canal

É possível fechar um canal com a função `close(ch)`:

```go
close(ch)
```

Fechar um canal indica que nenhum outro valor será enviado para ele. Isso é útil quando você tem um produtor e múltiplos consumidores e deseja sinalizar que os dados acabaram. Ao tentar **ler** de um canal fechado, o programa retorna o valor zero do tipo (como `0`, `""` ou `nil`). Tentar **enviar** para um canal fechado causa um `panic`.

---

## Range em canais

Você pode iterar sobre os valores de um canal usando `range`, mas o canal precisa ser fechado para que o `range` saiba quando parar:

```go
for valor := range ch {
	fmt.Println(valor)
}
```

## Channels com buffer

Você pode criar canais com buffer para permitir que vários valores sejam enviados sem bloqueio imediato:

```go
ch := make(chan int, 3)
ch <- 1
ch <- 2
ch <- 3
```

Neste exemplo, até 3 valores podem ser armazenados no canal antes que a operação de envio bloqueie. A leitura ainda pode ocorrer a qualquer momento.

## Channels unidirecionais

Go permite especificar canais apenas de envio ou apenas de recebimento em parâmetros de função:

```go
func enviar(ch chan<- int) {
	ch <- 10
}

func receber(ch <-chan int) {
	fmt.Println(<-ch)
}
```

Isso é útil para garantir que certas funções só realizem um tipo de operação com o canal, o que melhora a segurança e a legibilidade do código.

## `select` em Go: o que é, como funciona e por que usar

A palavra-chave `select` em Go é usada para **aguardar múltiplas operações de canal ao mesmo tempo**, permitindo que a goroutine continue com aquela operação que estiver pronta primeiro. É semelhante ao `switch`, mas funciona com canais.

Isso é especialmente útil quando você tem **diversas goroutines concorrentes** produzindo dados em canais diferentes, ou quando você quer implementar **timeouts**, **cancelamentos** ou **espera dinâmica** por mensagens.

### Estrutura básica do `select`

A estrutura de um `select` é:

```go
select {
case valor := <-canal1:
    // operação se canal1 tiver algo pronto
case canal2 <- valor:
    // operação se canal2 estiver pronto para receber
default:
    // operação se nenhum canal estiver pronto (opcional)
}
```

Cada `case` deve conter uma operação de canal, seja uma leitura (`<-canal`) ou uma escrita (`canal <- valor`).

O `select` bloqueia até que **pelo menos um dos canais** esteja pronto. Quando isso acontece, ele executa um dos cases prontos — se mais de um estiver pronto ao mesmo tempo, a escolha é feita **aleatoriamente**, distribuída uniformemente para evitar viés.

## Exemplo prático: escutando múltiplos canais

Vamos supor que temos duas goroutines simulando serviços externos, e queremos receber a resposta de quem retornar primeiro.

```go
package main

import (
	"fmt"
	"time"
)

func buscarUsuario(ch chan string) {
	time.Sleep(2 * time.Second)
	ch <- "usuário encontrado"
}

func buscarProduto(ch chan string) {
	time.Sleep(1 * time.Second)
	ch <- "produto encontrado"
}

func main() {
	chUsuario := make(chan string)
	chProduto := make(chan string)

	go buscarUsuario(chUsuario)
	go buscarProduto(chProduto)

	select {
	case resposta := <-chUsuario:
		fmt.Println("Resposta do usuário:", resposta)
	case resposta := <-chProduto:
		fmt.Println("Resposta do produto:", resposta)
	}
}
```

### Explicação detalhada

1. Criamos dois canais de string, um para cada operação assíncrona.
    
2. Disparamos duas goroutines que simulam chamadas com tempo de resposta diferente.
    
3. O `select` escuta os dois canais.
    
4. Como `buscarProduto` tem delay menor, ele envia primeiro. O `select` então executa o `case` correspondente.
    

O uso do `select` evita o bloqueio completo do programa por causa de uma operação mais lenta, e nos permite reagir ao que estiver disponível.

---

## Timeout com `select` e `time.After`

Você pode usar o `select` para implementar timeouts. A função `time.After(duração)` retorna um canal que envia um valor depois de um tempo.

Exemplo:

```go
select {
case msg := <-ch:
	fmt.Println("Mensagem recebida:", msg)
case <-time.After(2 * time.Second):
	fmt.Println("Timeout: operação demorou demais")
}
```

Aqui, o `select` vai esperar até que:

- A mensagem seja recebida em `ch`, ou
    
- Se passem 2 segundos, o que ativará o `case` de timeout.
    

Essa é uma forma extremamente eficiente e idiomática de implementar tempo limite em operações assíncronas, como requisições HTTP, chamadas a banco, etc.

---

## Cancelamento com canais

Outra aplicação muito comum do `select` é usar canais como mecanismo de cancelamento.

Imagine que você quer cancelar uma tarefa longa caso o usuário feche a conexão. Você pode usar um canal `done` para isso.

```go
func processar(dados chan int, done chan struct{}) {
	for {
		select {
		case valor := <-dados:
			fmt.Println("Processando:", valor)
		case <-done:
			fmt.Println("Cancelado.")
			return
		}
	}
}
```

Nesse exemplo:

- O canal `dados` fornece informações a serem processadas.
    
- O canal `done` serve para sinalizar o cancelamento (quando alguém envia ou fecha esse canal).
    
- O `select` escuta os dois, e caso `done` seja ativado, a função retorna e a goroutine é encerrada de forma limpa.
    

---

## Comportamento determinístico e aleatoriedade no `select`

Quando **vários canais estão prontos ao mesmo tempo**, o Go escolhe um `case` pronto **aleatoriamente**, com distribuição uniforme.

Isso significa que você não pode confiar na ordem dos `case` dentro do `select`. A ordem do código-fonte **não influencia** a escolha do case a ser executado.

Para garantir prioridade ou controle mais rígido, você precisa usar lógica adicional ou múltiplos selects encadeados com condições.

---

## O `default` no `select`

O `default` é executado **imediatamente** se nenhum dos outros `case` estiver pronto. Isso transforma o `select` em algo não bloqueante.

Exemplo:

```go
select {
case msg := <-ch:
	fmt.Println("Recebido:", msg)
default:
	fmt.Println("Nenhuma mensagem disponível agora")
}
```

Útil para:

- Verificar se há dados disponíveis sem bloquear
    
- Fazer polls com fallback
    
- Executar tarefas de "background" enquanto canais estão inativos
    

---

## Considerações e boas práticas

1. **Evite deadlocks:** se nenhum canal estiver pronto e não houver `default`, o `select` bloqueia indefinidamente. Use `time.After` ou canais de controle para evitar isso.
    
2. **Cuidado ao fechar canais:** nunca escreva em um canal fechado.
    
3. **Combine `select` com `for`:** para escutar canais continuamente.
    
4. **Prefira sinais com canais ao invés de variáveis booleanas com mutex.** Os canais são mais idiomáticos e seguros em Go.
    
5. **Use `context.Context` para cancelamento em sistemas mais complexos.** Ele é baseado em canais internamente e fornece deadline, timeout e cancelamento em cascata.
    
