---
tags:
  - Tecnologia
  - DataStructures
  - SoftwareArchitecture
  - Security
  - GoLang
---
A **stack** é uma área de memória gerenciada automaticamente, usada para armazenar variáveis locais, argumentos de funções, e informações de controle de execução. Ela segue uma estrutura **LIFO** (Last In, First Out), o que significa que o último elemento adicionado é o primeiro a ser removido. Vamos detalhar como ela funciona e como os dados são armazenados e gerenciados.

---

### **O que é a Stack?**

A stack é uma região de memória usada para:

1. **Gerenciar chamadas de funções**.
2. **Armazenar variáveis locais** de forma eficiente e limitada ao escopo de execução.
3. **Controlar o fluxo de execução** por meio de endereços de retorno.

---

### **Características da Stack**

1. **Organização LIFO**:
    - Cada chamada de função adiciona (empilha) um "frame" na stack.
    - Quando a função termina, o frame é removido (desempilhado).
2. **Rápida e Determinística**:
    - A alocação e a liberação são automáticas, seguindo o modelo de escopo.
3. **Memória Limitada**:
    - O tamanho da stack é fixo e geralmente pequeno. Excedê-lo causa um **stack overflow**.

---

### **Como os Dados São Armazenados na Stack?**

A cada chamada de função, um **frame** (bloco de ativação) é criado no topo da stack. Esse frame contém:

1. **Endereço de Retorno**:
    - Indica onde o programa deve continuar após a função terminar.
2. **Argumentos da Função**:
    - Os valores passados para a função.
3. **Variáveis Locais**:
    - Declaradas dentro da função e válidas apenas durante sua execução.

Exemplo em Go:

```go
func add(a, b int) int {
    result := a + b // 'result' está na stack
    return result
}

func main() {
    sum := add(5, 7) // 'sum' também está na stack
    fmt.Println(sum)
}
```

#### Como a Stack é preenchida:

1. Quando `main` é chamado:
    - Um frame para `main` é criado no topo da stack.
2. Quando `add` é chamado:
    - Um novo frame é empilhado contendo:
        - Os argumentos `a` e `b`.
        - A variável local `result`.
        - O endereço para retornar a `main`.
3. Quando `add` termina:
    - Seu frame é removido.
    - O controle volta para `main`.
4. Quando `main` termina:
    - Seu frame é removido e o programa encerra.

---

### **Vantagens da Stack**

1. **Eficiência**:
    - Operações de empilhar/desempilhar são rápidas devido à simplicidade do modelo LIFO.
2. **Memória Temporária**:
    - Perfeito para variáveis cujo tempo de vida é curto e limitado ao escopo.
3. **Sem Fragmentação**:
    - A memória é liberada em ordem reversa, garantindo consistência.

---

### **Limitações da Stack**

1. **Tamanho Restrito**:
    - O tamanho da stack é fixo, geralmente configurado pelo sistema operacional. Excedê-lo causa **stack overflow**.
    - Exemplo:
        
        ```go
        func recurse(n int) int {
            return recurse(n + 1) // Recursão sem fim pode causar stack overflow
        }
        
        func main() {
            recurse(1)
        }
        ```
        
2. **Apenas Escopo Local**:
    - Dados na stack desaparecem assim que o escopo termina. Não é possível compartilhar variáveis entre funções usando a stack.

---

### **Diferenças entre Stack e Heap**

| Característica    | Stack              | Heap                                  |
| ----------------- | ------------------ | ------------------------------------- |
| **Organização**   | LIFO               | Não sequencial                        |
| **Velocidade**    | Muito rápida       | Mais lenta                            |
| **Gerenciamento** | Automático         | Gerenciado pelo [[Garbage Collector]] |
| **Tempo de Vida** | Limitado ao escopo | Prolongado                            |
| **Tamanho**       | Fixo               | Dinâmico                              |

---

### **Exemplo Prático da Stack**

Vamos ilustrar como a stack funciona com um programa em Go:

```go
package main

import "fmt"

func multiply(a, b int) int {
    product := a * b // Variável local armazenada na stack
    return product
}

func addAndMultiply(x, y, z int) int {
    sum := x + y // Variável local armazenada na stack
    return multiply(sum, z)
}

func main() {
    result := addAndMultiply(2, 3, 4) // Variável local armazenada na stack
    fmt.Println(result)
}
```

#### Processo da Stack:

1. Quando `main` é chamado:
    - Um frame é criado contendo `result`.
2. Quando `addAndMultiply` é chamado:
    - Um novo frame é empilhado contendo `x`, `y`, `z` e `sum`.
3. Quando `multiply` é chamado:
    - Outro frame é empilhado contendo `a`, `b` e `product`.
4. A execução segue o modelo LIFO:
    - `multiply` termina → seu frame é removido.
    - `addAndMultiply` termina → seu frame é removido.
    - `main` termina → seu frame é removido.

---

### **Conclusão**

A stack é ideal para armazenar dados temporários devido à sua velocidade e simplicidade. É usada automaticamente para chamadas de funções e gerenciamento de variáveis locais. Embora seja limitada em tamanho, sua eficiência e gerenciamento automático tornam-na uma ferramenta essencial em qualquer programa, incluindo os escritos em [[Go]].