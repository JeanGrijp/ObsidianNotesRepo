---
tags:
  - Tecnologia
  - GoLang
  - back-end
  - Produtividade
  - Database
---
### 🧠 **1. Entenda o Conceito**

Antes de aplicar, é importante entender **o porquê** dessa arquitetura existir.

- **Objetivo:** separar a lógica de negócio (domínio) de detalhes de infraestrutura (como banco de dados, APIs, UI, etc).
- **Componentes principais:**
    - **Domínio (Core):** onde vive a lógica de negócio.
    - **Portas (Ports):** interfaces que o domínio expõe (driven ports) ou que ele consome (driving ports).
    - **Adaptadores (Adapters):** implementações concretas dessas interfaces (ex: banco de dados, HTTP, CLI, etc).

> Imagine o domínio como o centro da aplicação. Tudo se conecta a ele por meio de portas.

---

### 📚 **2. Recursos para Estudo**

- 📖 [Livro] _"Arquitetura Limpa"_ do Uncle Bob (não é sobre hexagonal, mas ajuda a entender o propósito).
- 📘 Artigos recomendados:
    - “Hexagonal Architecture” – por Alistair Cockburn (criador da ideia)
    - Artigos no blog do Martin Fowler
- 🎥 YouTube:
    - Pesquise por: "Hexagonal Architecture Go" ou "Arquitetura Hexagonal explicada"

---

### 🛠️ **3. Como aplicar no seu projeto (Go)**

Como você está usando Go, essa arquitetura se encaixa muito bem.

**Estrutura inicial de pastas:**

```
/internal
  /core       --> lógica de negócio (entidades, usecases, interfaces)
  /adapters   --> banco de dados, HTTP, gRPC, etc
  /ports      --> interfaces (caso queira separá-las do core)
  /config
  /app        --> inicialização da aplicação, injeção de dependências
/cmd
  /main.go
```

**Exemplo prático:**

- Digamos que você tenha uma entidade `Pet`.
    - **Core:** `Pet`, `PetService` com métodos `Adopt`, `Vaccinate` etc.
    - **Porta:** interface `PetRepository` no core.
    - **Adaptador:** implementação SQLX de `PetRepository` no pacote `/adapters/postgres`.

---

### 🚧 **4. Passo a passo para migrar seu projeto**

1. **Isolar o domínio:** mova a lógica central para um módulo `/core`.
2. **Criar interfaces:** defina as portas que o domínio vai usar (ex: `UserRepository`, `Notifier`).
3. **Implementar adaptadores:** crie os adapters que implementam essas interfaces (ex: banco de dados, serviço de e-mail).
4. **Injeção de dependência:** monte tudo no `main.go` (ou numa camada `app`) conectando portas e adaptadores.

---

Se quiser, posso te ajudar a **refatorar seu projeto atual**, mostrando um exemplo de estrutura e criando um plano de migração detalhado com base nas features que você já tem. Quer?