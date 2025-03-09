---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
aliases:
  - github.com/JeanGrijp/go-datastructures/pkg/stack
---
# O que é uma Stack?

Uma **Stack** (Pilha) é uma estrutura de dados baseada no princípio **LIFO** (_Last In, First Out_), ou seja, o último elemento adicionado é o primeiro a ser removido. Essa estrutura é útil em diversos cenários, como:

- Implementação de chamadas de função em linguagens de programação
- Algoritmos de busca e backtracking (ex.: DFS - _Depth First Search_)
- Gerenciamento de histórico de navegação
- Desfazer (_undo_) e refazer (_redo_) em editores de texto

As principais operações de uma pilha são:

- **Push**: Adiciona um elemento ao topo da pilha.
- **Pop**: Remove e retorna o elemento do topo da pilha.
- **Values**: Retorna todos os elementos armazenados na pilha.

---

## Implementação da Stack em Go

A seguir, temos a implementação de uma pilha em [[Go]] utilizando uma **lista encadeada**:

```go
package stack

// Definição do nó da pilha
type Node struct {
	Value any   // Valor armazenado no nó
	Next  *Node // Ponteiro para o próximo nó
}

// Estrutura da pilha
type Stack struct {
	first *Node // Ponteiro para o topo da pilha
}

// Push: Adiciona um elemento ao topo da pilha
func (s *Stack) Push(value any) {
	s.first = &Node{Value: value, Next: s.first}
}

// Pop: Remove e retorna o elemento do topo da pilha
func (s *Stack) Pop() (any, bool) {
	if s.first == nil {
		return nil, false // Retorna nil e false se a pilha estiver vazia
	} else {
		value := s.first.Value // Guarda o valor do topo
		s.first = s.first.Next // Move o topo para o próximo nó
		return value, true    // Retorna o valor removido e true
	}
}

// Values: Retorna todos os elementos da pilha
func (s *Stack) Values() any {
	if s.first == nil {
		return nil // Retorna nil se a pilha estiver vazia
	}
	var values []any
	for aux := s.first; aux != nil; aux = aux.Next {
		values = append(values, aux.Value)
	}
	return values
}
```

---

## Explicação do Código

### 1. **Definição da Estrutura `Node`**

```go
type Node struct {
	Value any   // Armazena o valor do nó
	Next  *Node // Ponteiro para o próximo nó
}
```

Cada elemento da pilha é representado por um **nó** (`Node`). Ele armazena um **valor** e um **ponteiro** para o próximo nó na pilha.

### 2. **Definição da Estrutura `Stack`**

```go
type Stack struct {
	first *Node // Ponteiro para o topo da pilha
}
```

A pilha (`Stack`) mantém um ponteiro chamado `first`, que aponta para o **último elemento inserido**.

### 3. **Função `Push(value any)`**

```go
func (s *Stack) Push(value any) {
	s.first = &Node{Value: value, Next: s.first}
}
```

O método `Push` **adiciona um novo elemento** ao topo da pilha:

- Um novo nó é criado com o valor passado.
- O ponteiro `Next` desse novo nó aponta para o **nó que era o topo antes**.
- O ponteiro `first` agora aponta para esse novo nó.

### 4. **Função `Pop() (any, bool)`**

```go
func (s *Stack) Pop() (any, bool) {
	if s.first == nil {
		return nil, false
	} else {
		value := s.first.Value
		s.first = s.first.Next
		return value, true
	}
}
```

O método `Pop` **remove e retorna** o elemento do topo:

- Se a pilha estiver vazia, retorna `nil` e `false`.
- Caso contrário, guarda o valor do topo, atualiza `first` para o próximo nó e retorna o valor removido junto com `true`.

### 5. **Função `Values() any`**

```go
func (s *Stack) Values() any {
	if s.first == nil {
		return nil
	}
	var values []any
	for aux := s.first; aux != nil; aux = aux.Next {
		values = append(values, aux.Value)
	}
	return values
}
```

O método `Values` retorna **todos os valores da pilha**:

- Percorre os nós da pilha e armazena os valores em um slice (`[]any`).
- Retorna esse slice contendo todos os elementos.

---

## Exemplo de Uso

Aqui está um exemplo de como utilizar essa implementação da pilha:

```go
package main

import (
	"fmt"
	"github.com/JeanGrijp/go-datastructures/pkg/stack"
)

func main() {
	s := stack.Stack{}

	s.Push(1)
	s.Push(2)
	s.Push(3)
	fmt.Println("Valores na Stack:", s.Values()) // [3 2 1]

	val, ok := s.Pop()
	fmt.Println("Elemento removido:", val, ok) // 3 true
	fmt.Println("Valores após Pop:", s.Values()) // [2 1]
}
```

---

## Conclusão

Essa implementação de pilha em Go utiliza **listas encadeadas**, permitindo inserções e remoções eficientes no topo da pilha. O código segue boas práticas e usa **tipos genéricos (`any`)**, tornando-o flexível para armazenar qualquer tipo de dado.

Se quiser aprimorar a implementação, você pode adicionar funcionalidades como:

- Um método `IsEmpty()` para verificar se a pilha está vazia.
- Um método `Size()` para contar o número de elementos na pilha.
- Melhorias no retorno do método `Values()` para que ele sempre retorne `[]any` ao invés de `any`.

Essa estrutura é muito útil para **gerenciamento de memória, processamento de expressões matemáticas e navegação em algoritmos recursivos**. 🚀