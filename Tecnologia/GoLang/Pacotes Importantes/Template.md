---
tags:
  - Tecnologia
  - GoLang
---
Um **template** em [[Go]] é um mecanismo para gerar saídas dinâmicas a partir de um modelo predefinido, usando os pacotes `text/template` (para texto) e `html/template` (para HTML seguro). Ele permite interpolação de dados, controle de fluxo (`if`, `range`), e manipulação de estruturas como [[structs]] e listas. É amplamente utilizado para renderizar HTML em aplicações web, evitando injeção de código malicioso. Templates podem ser definidos diretamente no código ou carregados de arquivos externos.

### 📌 Exemplo: Gerando um Relatório Simples

```go
package main

import (
	"os"
	"text/template"
)

type Relatorio struct {
	Nome    string
	Vendas  int
	Meta    int
}

func main() {
	// Criando um template para relatório de vendas
	tmpl := `Relatório de Vendas
-------------------
Vendedor: {{.Nome}}
Vendas Realizadas: {{.Vendas}}
Meta: {{.Meta}}
{{if ge .Vendas .Meta}}Parabéns! Meta atingida! 🎉{{else}}Ainda falta {{sub .Meta .Vendas}} vendas para a meta. Continue assim! 💪{{end}}
`

	// Criando e registrando uma função personalizada para subtração
	funcMap := template.FuncMap{
		"sub": func(a, b int) int { return a - b },
	}

	// Criando o template com a função customizada
	tmplParsed, err := template.New("relatorio").Funcs(funcMap).Parse(tmpl)
	if err != nil {
		panic(err)
	}

	// Dados do vendedor
	dados := Relatorio{"Jean", 25, 30}

	// Executando o template com os dados
	err = tmplParsed.Execute(os.Stdout, dados)
	if err != nil {
		panic(err)
	}
}
```

### 🔹 Saída esperada:

```
Relatório de Vendas
-------------------
Vendedor: Jean
Vendas Realizadas: 25
Meta: 30
Ainda falta 5 vendas para a meta. Continue assim! 💪
```

Este exemplo mostra como usar templates para gerar relatórios dinâmicos e incluir lógica condicional (`if`) e funções personalizadas (`sub`). 🚀

Pacote importante usado no exemplo [[os]].

### 🔍 **Entendendo `template.Must` no Go** 🚀

No Go, `template.Must` é uma **função auxiliar** do pacote `text/template` e `html/template` usada para **lidar com erros ao analisar (parse) templates**.

---

## 📌 **O problema sem `template.Must`**

Quando chamamos `template.ParseFiles()`, ele pode **retornar um erro** se o template estiver mal formatado ou não existir. Normalmente, precisaríamos fazer algo assim:

```go
package main

import (
	"html/template"
	"log"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	// Tentando carregar o template e verificando erro
	tmpl, err := template.ParseFiles("template.html")
	if err != nil {
		http.Error(w, "Erro ao carregar template", http.StatusInternalServerError)
		return
	}

	tmpl.Execute(w, nil)
}

func main() {
	http.HandleFunc("/", handler)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

🔹 **Problema**: Temos que lidar com `err` manualmente.

---

## 📌 **O que `template.Must` faz?**

`template.Must` **encapsula** a verificação de erro e **panica** (encerra o programa) se houver erro. Isso simplifica o código.

### ✏ **Exemplo usando `template.Must`**

```go
package main

import (
	"html/template"
	"log"
	"net/http"
)

// Carregamos o template no início e verificamos erro automaticamente
var tmpl = template.Must(template.ParseFiles("template.html"))

func handler(w http.ResponseWriter, r *http.Request) {
	tmpl.Execute(w, nil) // Agora não precisamos verificar erro aqui
}

func main() {
	http.HandleFunc("/", handler)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

✅ **Se `ParseFiles()` falhar, `Must()` interrompe a execução imediatamente**. Isso evita que o programa continue rodando com um estado inconsistente.

---

## 📌 **Quando usar `template.Must`?**

- Quando o **template deve ser carregado no início do programa**.
- Quando um **erro no template significa que o programa não deve continuar rodando**.
- **Melhora a organização** removendo a necessidade de lidar com `err` repetidamente.

🔹 **Quando **não** usar?**

- Se você precisar **carregar templates dinamicamente** durante a execução.
- Se o programa precisar continuar rodando mesmo que um template falhe.

✅ `template.Must` simplifica o código e evita verificações manuais de erro.  
✅ Se o template falhar ao carregar, o programa **encerra imediatamente**.  
✅ Ideal para **templates carregados na inicialização**.


### 🚀 **Templates + Web Server em Go (HTML dinâmico)** 🌍

No Go, podemos usar **templates** para gerar **páginas HTML dinâmicas** dentro de um **servidor web**. Isso é muito útil para criar aplicações web sem precisar de um framework pesado.

---

## 📌 **O que vamos construir?**

Criamos um **servidor HTTP** em Go que renderiza uma **página HTML dinâmica** usando o pacote `html/template`. O usuário acessa uma URL e o servidor responde com um HTML gerado a partir de um **template dinâmico**.

---

## 🔧 **Passo 1: Criando o Servidor HTTP**

Primeiro, criamos um **servidor web básico** que responde com um template.

### 📜 **Código do Servidor (`main.go`)**

```go
package main

import (
	"html/template"
	"net/http"
)

type Dados struct {
	Nome    string
	Titulo  string
	Mensagem string
}

func handler(w http.ResponseWriter, r *http.Request) {
	// Criando um template HTML
	tmpl := template.Must(template.ParseFiles("template.html"))

	// Dados dinâmicos
	data := Dados{
		Nome:    "Jean",
		Titulo:  "Bem-vindo ao meu site!",
		Mensagem: "Essa página foi gerada dinamicamente com Go!",
	}

	// Renderizando o template e enviando ao cliente
	tmpl.Execute(w, data)
}

func main() {
	http.HandleFunc("/", handler) // Define a rota "/"
	http.ListenAndServe(":8080", nil) // Inicia o servidor na porta 8080
}
```

✅ **Esse servidor responde na porta `8080` e renderiza um template HTML com dados dinâmicos.**

---

## 🔧 **Passo 2: Criando o Template HTML (`template.html`)**

Agora criamos um arquivo `template.html` para ser renderizado pelo servidor.

```html
<!DOCTYPE html>
<html lang="pt">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{.Titulo}}</title>
</head>
<body>
    <h1>{{.Titulo}}</h1>
    <p>Olá, {{.Nome}}!</p>
    <p>{{.Mensagem}}</p>
</body>
</html>
```

---

## 🎯 **Rodando o Servidor**

1️⃣ Salve os arquivos `main.go` e `template.html` no mesmo diretório.  
2️⃣ No terminal, execute:

```sh
go run main.go
```

3️⃣ Abra no navegador:  
👉 [`http://localhost:8080`](http://localhost:8080/)

🔹 **Saída esperada no navegador**:

```
Bem-vindo ao meu site!
Olá, Jean!
Essa página foi gerada dinamicamente com Go!
```

---

## 🔥 **Avançando: Trabalhando com Listas e Loops**

Podemos passar **listas** para um template e iterar com `{{range}}`.

### ✏ **Alterando `Dados` para incluir uma lista de tarefas**

```go
type Dados struct {
	Nome     string
	Titulo   string
	Mensagem string
	Tarefas  []string
}
```

### ✏ **Modificando `handler` para incluir tarefas**

```go
func handler(w http.ResponseWriter, r *http.Request) {
	tmpl := template.Must(template.ParseFiles("template.html"))

	data := Dados{
		Nome:    "Jean",
		Titulo:  "Lista de Tarefas",
		Mensagem: "Aqui estão suas tarefas pendentes:",
		Tarefas:  []string{"Aprender Go", "Criar um projeto", "Ler documentação"},
	}

	tmpl.Execute(w, data)
}
```

### ✏ **Modificando `template.html` para exibir a lista**

```html
<h1>{{.Titulo}}</h1>
<p>Olá, {{.Nome}}!</p>
<p>{{.Mensagem}}</p>

<ul>
    {{range .Tarefas}}
        <li>{{.}}</li>
    {{end}}
</ul>
```

🔹 **Agora, a página exibe uma lista dinâmica de tarefas.** ✅

---

## 🔥 **Adicionando um Formulário HTML e Pegando Dados do Usuário**

Agora, vamos permitir que o usuário **envie seu nome** por um formulário.

### ✏ **Modificando `handler` para pegar dados do formulário**

```go
func handler(w http.ResponseWriter, r *http.Request) {
	tmpl := template.Must(template.ParseFiles("template.html"))

	// Pegando o nome enviado pelo usuário (query param)
	nome := r.URL.Query().Get("nome")
	if nome == "" {
		nome = "Visitante"
	}

	data := Dados{
		Nome:    nome,
		Titulo:  "Página Personalizada",
		Mensagem: "Seja bem-vindo!",
	}

	tmpl.Execute(w, data)
}
```

### ✏ **Modificando `template.html` para incluir um formulário**

```html
<h1>{{.Titulo}}</h1>
<p>Olá, {{.Nome}}!</p>
<p>{{.Mensagem}}</p>

<form action="/" method="GET">
    <label>Digite seu nome:</label>
    <input type="text" name="nome">
    <button type="submit">Enviar</button>
</form>
```

### 🔹 **Como testar?**

1️⃣ Inicie o servidor:

```sh
go run main.go
```

2️⃣ Acesse no navegador:  
👉 [`http://localhost:8080`](http://localhost:8080/)  
3️⃣ Digite um nome no formulário e clique em **Enviar**  
🔹 O nome será exibido na página!

✅ Criamos um **web server em Go** usando `net/http`.  
✅ Usamos **templates HTML dinâmicos** com `html/template`.  
✅ Adicionamos **listas e loops** para exibir tarefas.  
✅ Criamos um **formulário interativo** para capturar dados do usuário.

------

### 🚀 **Gerando um Relatório de Vendas em PDF para um Petshop usando Templates no Go** 🐶📄

Vamos criar um **servidor web em Go** que gera um **relatório de vendas em PDF** para um petshop, usando templates para organizar o conteúdo.

---

## 📌 **O que vamos construir?**

✅ Criamos um **servidor HTTP**  
✅ Usamos `html/template` para **gerar o conteúdo do relatório**  
✅ Convertamos esse conteúdo para um **arquivo PDF** usando `github.com/jung-kurt/gofpdf`

---

## 🔧 **Passo 1: Instalar a Biblioteca `gofpdf`**

`gofpdf` permite gerar arquivos PDF facilmente no Go.

```sh
go get github.com/jung-kurt/gofpdf
```

---

## 📜 **Código do Servidor (`main.go`)**

```go
package main

import (
	"bytes"
	"html/template"
	"log"
	"net/http"
	"time"

	"github.com/jung-kurt/gofpdf"
)

// Estrutura para armazenar os dados do relatório
type Venda struct {
	Data      string
	Produto   string
	Quantidade int
	Preco     float64
}

type Relatorio struct {
	Petshop  string
	Data     string
	Vendas   []Venda
	Total    float64
}

func gerarPDF(relatorio Relatorio) []byte {
	// Criando um novo PDF
	pdf := gofpdf.New("P", "mm", "A4", "")
	pdf.SetFont("Arial", "", 12)

	// Adicionando página
	pdf.AddPage()
	pdf.Cell(40, 10, "Relatório de Vendas - "+relatorio.Petshop)
	pdf.Ln(10)

	// Cabeçalho
	pdf.Cell(40, 10, "Data: "+relatorio.Data)
	pdf.Ln(10)

	// Tabela de vendas
	for _, venda := range relatorio.Vendas {
		pdf.Cell(40, 10, venda.Data+" - "+venda.Produto)
		pdf.Cell(30, 10, "Qtd: "+string(venda.Quantidade))
		pdf.Cell(40, 10, "R$ "+string(venda.Preco))
		pdf.Ln(8)
	}

	// Total de vendas
	pdf.Ln(10)
	pdf.Cell(40, 10, "Total: R$ "+string(relatorio.Total))

	// Salvando o PDF em memória
	var buf bytes.Buffer
	err := pdf.Output(&buf)
	if err != nil {
		log.Fatal("Erro ao gerar PDF:", err)
	}

	return buf.Bytes()
}

func handler(w http.ResponseWriter, r *http.Request) {
	// Simulando vendas do petshop
	vendas := []Venda{
		{"01/02/2024", "Ração Premium", 2, 99.90},
		{"02/02/2024", "Brinquedo para cachorro", 3, 19.99},
		{"03/02/2024", "Coleira", 1, 35.00},
	}

	// Calculando total
	total := 0.0
	for _, v := range vendas {
		total += v.Preco * float64(v.Quantidade)
	}

	// Criando estrutura do relatório
	relatorio := Relatorio{
		Petshop: "Pet Feliz",
		Data:    time.Now().Format("02/01/2006"),
		Vendas:  vendas,
		Total:   total,
	}

	// Gerando PDF
	pdfBytes := gerarPDF(relatorio)

	// Enviando resposta como arquivo PDF
	w.Header().Set("Content-Type", "application/pdf")
	w.Header().Set("Content-Disposition", "attachment; filename=relatorio_petshop.pdf")
	w.Write(pdfBytes)
}

func main() {
	http.HandleFunc("/relatorio", handler)
	log.Println("Servidor rodando em http://localhost:8080")
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

---

## 🎯 **Como usar o servidor?**

1️⃣ Execute o servidor:

```sh
go run main.go
```

2️⃣ Acesse no navegador: 👉 [`http://localhost:8080/relatorio`](http://localhost:8080/relatorio)  
3️⃣ O navegador **baixará automaticamente o relatório em PDF**! 🎉

✅ Criamos um **web server** que gera **relatórios PDF** com templates dinâmicos.  
✅ Usamos **`gofpdf`** para formatar e salvar o PDF.  
✅ Simulamos um **relatório de vendas de um petshop**.