---
tags:
  - Tecnologia
  - GoLang
---
# 📘 O que são Structs em Go?

Structs são tipos personalizados que permitem agrupar diversos campos (variáveis) em uma única unidade lógica. Cada campo dentro de uma struct pode ter seu próprio tipo, permitindo uma estrutura flexível e organizada de dados.

Structs em [[Go]] equivalem a objetos sem herança ou métodos integrados. Você pode pensar nelas como **modelos para representar entidades**, semelhantes às classes em linguagens orientadas a objetos.

---

# 🛠️ Como criar uma Struct?

Para declarar uma struct em Go, utilize o comando `type` seguido do nome e dos campos da struct:

```go
package main

import "fmt"

// Pessoa representa uma pessoa com nome e idade
type Pessoa struct {
    Nome  string
    Idade int
}

func main() {
    // Criando uma instância da struct Pessoa
    p := Pessoa{
        Nome:  "Jean",
        Idade: 30,
    }

    fmt.Println(p) // {Jean 30}
}
```

> Aqui criamos uma struct chamada `Pessoa` com dois campos (`Nome` e `Idade`).

---

## 📌 Formas alternativas de inicializar Structs

Além da inicialização padrão acima, existem outras formas:

**Inicialização sem nomes explícitos dos campos:**

```go
p := Pessoa{"Jean", 30}
```

**Inicialização parcial (campos não declarados ficam com valor padrão):**

```go
p := Pessoa{Nome: "Jean"} // Idade fica com valor zero (0)
```

**Usando `new()` (retorna um ponteiro):**

```go
p := new(Pessoa)
p.Nome = "Jean"
p.Idade = 30
```

---

# 🎯 Métodos de Structs em Go

Go permite associar funções específicas às structs, criando **métodos**. São funções que recebem um **receiver** (a struct associada), permitindo que você chame métodos diretamente sobre instâncias daquela struct.

### Exemplo básico:

```go
package main

import "fmt"

// Pessoa representa uma pessoa com nome e idade
type Pessoa struct {
    Nome  string
    Idade int
}

// Método associado à struct Pessoa
func (p Pessoa) Cumprimentar() {
    fmt.Printf("Olá, eu sou %s e tenho %d anos.\n", p.Nome, p.Idade)
}

func main() {
    pessoa := Pessoa{"Jean", 30}
    pessoa.Cumprimentar() // Olá, eu sou Jean e tenho 30 anos.
}
```

- `(p Pessoa)` é o **receiver**, que é a instância da struct que chama o método.

---

## 📌 Métodos com receiver por valor vs. receiver por ponteiro

Existem duas formas de definir métodos em structs:

### 1. Receivers por valor (cópia):

Recebem uma cópia da struct. Alterações feitas não afetam o original.

```go
func (p Pessoa) Aniversario() {
    p.Idade++
    fmt.Println("Idade dentro do método:", p.Idade)
}
```

- Ao chamar o método:

```go
pessoa := Pessoa{"Jean", 30}
pessoa.Aniversario()           // Idade dentro do método: 31
fmt.Println(pessoa.Idade)      // 30 (não alterou!)
```

### 2. Receivers por ponteiro (referência):

Usando ponteiros, você pode modificar o valor original.

```go
func (p *Pessoa) Aniversario() {
    p.Idade++
    fmt.Println("Idade dentro do método:", p.Idade)
}
```

- Agora, ao chamar:

```go
pessoa := Pessoa{"Jean", 30}
pessoa.Aniversario()           // Idade dentro do método: 31
fmt.Println(pessoa.Idade)      // 31 (valor alterado!)
```

> **Dica**: Para modificar dados internos da struct, use sempre receiver por [ponteiro](Ponteiros.md) (`*Pessoa`).

---

## 📌 Quando usar Receivers por valor ou Receivers por ponteiro?

|Receiver por valor ✅|Receiver por ponteiro ✅|
|---|---|
|Não precisa alterar valores internos|Precisa alterar valores internos|
|Struct pequena (baixo custo copiar)|Struct grande (evitar cópias caras)|
|Métodos que apenas exibem valores|Métodos que alteram valores internos|

---

## 🔗 Composição de Structs (Herança de campos)

Go não possui herança clássica (como Java ou C#), mas você pode usar **composição** para reutilizar structs em outras structs.

### Exemplo de composição:

```go
package main

import "fmt"

// Animal representa atributos básicos de animais
type Animal struct {
    Nome string
}

// Cachorro herda atributos de Animal
type Cachorro struct {
    Animal    // composição
    Raca string
}

func main() {
    dog := Cachorro{
        Animal: Animal{Nome: "Rex"},
        Raca:   "Golden Retriever",
    }

    fmt.Println(dog.Nome) // Rex (acessado diretamente por composição)
    fmt.Println(dog.Raca) // Golden Retriever
}
```

> **Composição**: O campo `Animal` está embutido diretamente em `Cachorro`.

---

## 🚩 Dicas extras ao utilizar Structs e Métodos em Go:

- **Campos exportáveis** começam com letra maiúscula. Ex.: `Nome`, `Idade`.
- **Campos privados** começam com letra minúscula. Ex.: `nome`, `idade`.
- Métodos são definidos sempre fora do bloco da struct.
- Use receivers curtos, geralmente 1 ou 2 letras (`p` para Pessoa, `c` para Cachorro).

---

## 📌 Resumo da estrutura básica:

```go
// Declaração da struct
type MinhaStruct struct {
    Campo1 string
    Campo2 int
}

// Método com receiver por valor
func (m MinhaStruct) MetodoValor() {}

// Método com receiver por ponteiro
func (m *MinhaStruct) MetodoPonteiro() {}
```