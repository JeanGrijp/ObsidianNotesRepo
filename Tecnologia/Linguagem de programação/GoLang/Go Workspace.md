---
tags:
  - Tecnologia
  - GoLang
---
O **workspace** no [[Go]] é um recurso introduzido na versão **Go 1.18**, projetado para simplificar o desenvolvimento local de múltiplos módulos relacionados. Ele permite que você trabalhe facilmente em vários módulos simultaneamente, sem precisar publicar versões intermediárias ou usar diretivas como `replace` no arquivo `go.mod`.

---

## 📌 O que é um Go workspace?

Um **workspace** é um ambiente local que reúne diversos módulos Go para desenvolvimento simultâneo. Ele consiste principalmente em um arquivo `go.work` na raiz do seu workspace, que gerencia módulos que você deseja trabalhar diretamente na sua máquina.

O Go workspace permite que você:

- Desenvolva módulos locais em conjunto, sem precisar publicá-los ou ajustar versões manualmente.
- Evite o uso frequente de `replace` em `go.mod` ao referenciar módulos locais.
- Faça alterações simultâneas em vários módulos, com atualizações refletidas imediatamente no ambiente local.

---

## 📂 Como criar um workspace em Go (passo a passo)

Suponha que você tenha dois módulos separados:

```
├── projeto-a
│   ├── go.mod
│   └── main.go
└── projeto-b
    ├── go.mod
    └── main.go
```

Ambos precisam ser desenvolvidos juntos localmente.

### 1. Criando o arquivo `go.work`

Para iniciar o workspace, vá até o diretório pai dos módulos (`projeto-a` e `projeto-b`) e execute:

```bash
go work init ./projeto-a ./projeto-b
```

Isso gera o arquivo `go.work`:

```go
go 1.22

use (
    ./projeto-a
    ./projeto-b
)
```

### 2. Estrutura após criar o workspace:

```
├── projeto-a
│   ├── go.mod
│   └── main.go
├── projeto-b
│   ├── go.mod
│   └── main.go
└── go.work
```

A partir de agora, quando você executar comandos como `go run`, `go test`, `go build` dentro desse workspace, o Go considerará os módulos locais definidos em `go.work`.

---

## ⚙️ Como o workspace evita o uso de `replace` no `go.mod`?

Antes do workspace, para trabalhar com módulos locais simultaneamente, o mais comum era usar a diretiva `replace` em `go.mod`:

```go
module github.com/jeangomes/projeto-a

go 1.22

require github.com/jeangomes/projeto-b v0.1.0

replace github.com/jeangomes/projeto-b => ../projeto-b
```

O problema disso é que essa abordagem:

- É manual, sujeita a erros, e exige constante alteração no arquivo `go.mod`.
- Não é ideal para o desenvolvimento colaborativo, pois você precisaria lembrar de remover o `replace` antes de publicar o módulo.

Com o workspace, isso não é mais necessário, pois:

- O arquivo `go.work` informa automaticamente ao Go onde encontrar módulos locais, sem necessidade de editar `go.mod`.
- `go.mod` continua limpo e pronto para uso direto na publicação.
- Alterações locais são automaticamente reconhecidas pelo Go.

Desta forma, o `go.mod` ficaria limpo assim:

```go
module github.com/jeangomes/projeto-a

go 1.22

require github.com/jeangomes/projeto-b v0.1.0
```

O Go workspace "internamente substitui" o módulo pela versão local automaticamente durante o desenvolvimento, mas sem precisar mexer diretamente em `go.mod`.

---

## 🔍 Comandos úteis com workspaces em Go

- **Adicionar outro módulo ao workspace**:

```bash
go work use ./projeto-c
```

- **Remover módulo do workspace**:

```bash
go work edit -dropuse ./projeto-b
```

- **Verificar módulos ativos no workspace**:

```bash
go work edit -json
```

---

## 🚩 Benefícios do uso do workspace em resumo:

- **Simplicidade:** Não precisa gerenciar `replace`.
- **Produtividade:** Edição simultânea de múltiplos módulos locais.
- **Organização:** `go.mod` fica limpo e apenas versões oficiais são registradas nele.
- **Confiabilidade:** Menos risco de esquecer diretivas temporárias no controle de versão.