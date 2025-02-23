---
tags:
  - Tecnologia
  - GoLang
---
A **biblioteca `os`** no [[Go]] fornece funções essenciais para interagir com o sistema operacional, permitindo manipular arquivos, diretórios, variáveis de ambiente, processos e entradas/saídas.

---

## 🔹 Principais Funcionalidades

### 1️⃣ **Manipulação de Arquivos**

Criando, lendo e escrevendo arquivos.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// Criando um arquivo
	file, err := os.Create("exemplo.txt")
	if err != nil {
		panic(err)
	}
	defer file.Close()

	// Escrevendo no arquivo
	file.WriteString("Olá, mundo!\n")

	// Lendo o arquivo
	content, err := os.ReadFile("exemplo.txt")
	if err != nil {
		panic(err)
	}

	fmt.Println(string(content))
}
```

---

### 2️⃣ **Manipulação de Diretórios**

Criando e removendo diretórios.

```go
os.Mkdir("meu_diretorio", 0755) // Criar diretório
os.Remove("meu_diretorio")      // Remover diretório
```

A função `os.Mkdir("meu_diretorio", 0755)` cria um diretório chamado **"meu_diretorio"** e recebe dois parâmetros:

1️⃣ **"meu_diretorio"** → Nome do diretório a ser criado.  
2️⃣ **0755** → Permissões do diretório (octal).

---

## 🔹 Explicação sobre o **0755**

O segundo parâmetro define as **permissões do arquivo ou diretório** usando **notação octal** (base 8), semelhante ao que se usa em sistemas Unix/Linux.

A permissão `0755` pode ser dividida assim:

|Permissão|Dono (Owner)|Grupo (Group)|Outros (Others)|
|---|---|---|---|
|**7** (rwx)|✅ Leitura (r) ✅ Escrita (w) ✅ Execução (x)|-|-|
|**5** (r-x)|-|✅ Leitura (r) ❌ Escrita (-) ✅ Execução (x)||
|**5** (r-x)|-|✅ Leitura (r) ❌ Escrita (-) ✅ Execução (x)||

### 🔹 Como interpretar os números?

Cada número (de 0 a 7) representa um conjunto de permissões:

- **4** → Leitura (`r`)
- **2** → Escrita (`w`)
- **1** → Execução (`x`)
- **0** → Nenhuma permissão

Esses valores são somados para determinar a permissão final:

- `7` (**4+2+1**) → **Dono** pode ler, escrever e executar.
- `5` (**4+0+1**) → **Grupo** pode ler e executar.
- `5` (**4+0+1**) → **Outros** podem ler e executar.

---

## 🔹 Outros exemplos de permissões

|Octal|Significado|
|---|---|
|**0777**|Todos podem ler, escrever e executar|
|**0700**|Apenas o dono tem todas as permissões|
|**0644**|Dono pode ler/escrever, grupo e outros só leem|
|**0000**|Ninguém tem permissão|

🔹 **Observação**: O `0` inicial indica que o número está na **notação octal**.

---

## 🔹 Criando diretórios recursivamente (`os.MkdirAll`)

Se precisar criar subdiretórios automaticamente, use:

```go
os.MkdirAll("pasta1/pasta2/pasta3", 0755)
```

Isso cria toda a estrutura `pasta1/pasta2/pasta3`, sem erro se os diretórios anteriores não existirem.

---

🚀 **Resumo**:  
O segundo parâmetro da `os.Mkdir` define as permissões do diretório, seguindo a notação octal do Unix. No caso de `0755`, o dono tem **todas** as permissões, e os demais podem apenas **ler e executar**.

---

### 3️⃣ **Variáveis de Ambiente**

Obtendo e definindo variáveis de ambiente.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	os.Setenv("MINHA_VARIAVEL", "GoLang")
	valor := os.Getenv("MINHA_VARIAVEL")
	fmt.Println("Valor da variável:", valor)
}
```

Como **definir e obter variáveis de ambiente** usando o pacote `os`.

## 🟢 **O que são variáveis de ambiente?**

São valores armazenados no sistema operacional que podem ser acessados por programas em execução. Elas são úteis para armazenar configurações como **credenciais, caminhos de arquivos, configurações de conexão, entre outros**.

---

## 📝 **Explicação do código**

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// Definir uma variável de ambiente chamada "MINHA_VARIAVEL" com o valor "GoLang"
	os.Setenv("MINHA_VARIAVEL", "GoLang")

	// Obter o valor da variável de ambiente
	valor := os.Getenv("MINHA_VARIAVEL")

	// Exibir o valor da variável no console
	fmt.Println("Valor da variável:", valor)
}
```

---

### 🛠 **Passo a passo do código**

1️⃣ **Importação de pacotes**

```go
import (
	"fmt"
	"os"
)
```

- `fmt` → Usado para imprimir mensagens no terminal.
- `os` → A lib que já estou explicando.

---

2️⃣ **Definir uma variável de ambiente**

```go
os.Setenv("MINHA_VARIAVEL", "GoLang")
```

- A função `os.Setenv(nome, valor)` define uma variável de ambiente temporária chamada `"MINHA_VARIAVEL"` e atribui o valor `"GoLang"` a ela.
- **Importante**: Essa variável **existe apenas durante a execução do programa** e **não persiste** após o término.

---

3️⃣ **Obter o valor da variável**

```go
valor := os.Getenv("MINHA_VARIAVEL")
```

- A função `os.Getenv(nome)` retorna o valor da variável de ambiente **se ela existir**.
- Caso a variável **não exista**, a função retorna uma **string vazia** (`""`).

---

4️⃣ **Exibir o valor da variável**

```go
fmt.Println("Valor da variável:", valor)
```

- Exibe `"Valor da variável: GoLang"` no terminal.

---

## ⚠ **Atenção: Variáveis de ambiente não persistem**

Se rodar esse código e depois tentar obter a variável no terminal, ela **não estará disponível** porque foi definida apenas na execução do programa.

---

## 🔹 **Exemplo real: Usando variáveis de ambiente para senhas**

Em aplicações reais, **nunca** armazene senhas no código-fonte. Em vez disso, use variáveis de ambiente:

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// Pegando a senha de um banco de dados da variável de ambiente
	senha := os.Getenv("DB_PASSWORD")

	// Se a variável não estiver definida, mostrar uma mensagem
	if senha == "" {
		fmt.Println("Erro: variável de ambiente DB_PASSWORD não definida!")
	} else {
		fmt.Println("Conectando ao banco com a senha:", senha)
	}
}
```

🛑 **Nunca exponha senhas em logs!** Em aplicações reais, a senha deve ser usada apenas na conexão do banco.

---

- `os.Setenv("NOME", "VALOR")` → Define uma variável de ambiente **temporária**.
- `os.Getenv("NOME")` → Obtém o valor da variável (ou retorna `""` se não existir).
- **Não persistem após o término do programa**.
- **Muito usadas para configurações e credenciais** em aplicações Go.

---

### 4️⃣ **Trabalhando com Argumentos da Linha de Comando**

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	args := os.Args // Obtém os argumentos da linha de comando
	fmt.Println("Argumentos:", args)
}
```

Rodando `go run main.go teste`:

```
Argumentos: [/caminho/do/programa/main teste]
```

## 🟢 **Explicação básica**

### 🔹 Como rodar:

```sh
go run main.go teste argumento1 argumento2
```

### 🔹 Saída:

```
Argumentos: [/caminho/do/programa/main teste argumento1 argumento2]
```

- `os.Args[0]` → Sempre é o caminho do executável.
- `os.Args[1]` em diante → São os argumentos passados pelo usuário.

---

## 🟠 **Exemplo 1: Pegando um argumento específico**

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	if len(os.Args) < 2 {
		fmt.Println("Uso: go run main.go <nome>")
		return
	}

	nome := os.Args[1] // Pega o primeiro argumento após o nome do programa
	fmt.Println("Olá,", nome)
}
```

### 🔹 Como rodar:

```sh
go run main.go Jean
```

### 🔹 Saída:

```
Olá, Jean
```

---

## 🔵 **Exemplo 2: Somando números passados como argumento**

```go
package main

import (
	"fmt"
	"os"
	"strconv"
)

func main() {
	if len(os.Args) < 3 {
		fmt.Println("Uso: go run main.go <num1> <num2>")
		return
	}

	num1, err1 := strconv.Atoi(os.Args[1])
	num2, err2 := strconv.Atoi(os.Args[2])

	if err1 != nil || err2 != nil {
		fmt.Println("Erro: Argumentos devem ser números inteiros")
		return
	}

	soma := num1 + num2
	fmt.Println("Soma:", soma)
}
```

Pacote usado [[strconv]].
### 🔹 Como rodar:

```sh
go run main.go 10 20
```

### 🔹 Saída:

```
Soma: 30
```

---

## 🔴 **Exemplo 3: Verificando flags na linha de comando**

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	for _, arg := range os.Args[1:] { // Pula o nome do executável
		if arg == "--help" {
			fmt.Println("Uso: go run main.go [opções]")
			fmt.Println("Opções disponíveis:")
			fmt.Println("--help     Mostra esta ajuda")
			fmt.Println("--versao   Mostra a versão do programa")
			return
		} else if arg == "--versao" {
			fmt.Println("Versão 1.0.0")
			return
		}
	}

	fmt.Println("Nenhuma opção válida foi passada.")
}
```

### 🔹 Como rodar:

```sh
go run main.go --help
```

### 🔹 Saída:

```
Uso: go run main.go [opções]
Opções disponíveis:
--help     Mostra esta ajuda
--versao   Mostra a versão do programa
```

Ou:

```sh
go run main.go --versao
```

### 🔹 Saída:

```
Versão 1.0.0
```

---

## 🟣 **Exemplo 4: Criando um CLI básico**

Se quiser algo mais avançado para CLI, você pode usar a lib `flag` para tratar argumentos mais facilmente.

```go
package main

import (
	"flag"
	"fmt"
)

func main() {
	nome := flag.String("nome", "Usuário", "Nome a ser saudado")
	idade := flag.Int("idade", 30, "Idade da pessoa")
	flag.Parse()

	fmt.Printf("Olá, %s! Você tem %d anos.\n", *nome, *idade)
}
```

### 🔹 Como rodar:

```sh
go run main.go --nome=Jean --idade=25
```

### 🔹 Saída:

```
Olá, Jean! Você tem 25 anos.
```

---

- `os.Args` captura argumentos da linha de comando.
- `os.Args[0]` sempre contém o nome do executável.
- Use `len(os.Args)` para evitar erros ao acessar argumentos.
- `strconv.Atoi()` pode converter argumentos para números.
- `flag` facilita o manuseio de argumentos nomeados (`--nome=Jean`).

Para algo mais avançado, tipo um CLI profissional, podemos criar CLI usando a lib [[cobra]]? 🐍🔥

---

### 5️⃣ **Obter Informações do Sistema**

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	hostname, _ := os.Hostname()
	fmt.Println("Hostname:", hostname)

	uid := os.Getuid()
	fmt.Println("ID do usuário:", uid)
}
```

### 🖥️ **Obter Informações do Sistema com `os` no Go** 🚀

O pacote `os` no Go permite **interagir com o sistema operacional**, obtendo dados sobre o **hostname, ID de usuário, variáveis de ambiente, diretórios e muito mais**. Vou te mostrar mais detalhes e exemplos práticos! 🔥

---

## 🔹 **1. Obter o Nome da Máquina (`Hostname`)**

O **hostname** é o nome do computador na rede.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	hostname, err := os.Hostname()
	if err != nil {
		fmt.Println("Erro ao obter o hostname:", err)
		return
	}

	fmt.Println("Hostname:", hostname)
}
```

### 🔹 **Saída esperada:**

```
Hostname: meu-computador
```

---

## 🔹 **2. Obter o ID do Usuário (`Getuid`)**

No Linux/macOS, podemos obter o **ID do usuário** (UID).

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	uid := os.Getuid()
	fmt.Println("ID do usuário:", uid)
}
```

### 🔹 **Saída esperada no Linux/macOS:**

```
ID do usuário: 1001
```

⚠ **No Windows, `os.Getuid()` não está disponível!** Para evitar erros, pode-se verificar o sistema operacional:

```go
package main

import (
	"fmt"
	"os"
	"runtime"
)

func main() {
	if runtime.GOOS == "windows" {
		fmt.Println("Getuid não é suportado no Windows!")
	} else {
		fmt.Println("ID do usuário:", os.Getuid())
	}
}
```

---

## 🔹 **3. Obter Nome do Usuário Logado (`USER`)**

Outra maneira de obter o usuário logado é via variável de ambiente.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	user := os.Getenv("USER") // Linux/macOS
	if user == "" {
		user = os.Getenv("USERNAME") // Windows
	}

	fmt.Println("Usuário logado:", user)
}
```

### 🔹 **Saída esperada:**

```
Usuário logado: Jean
```

---

## 🔹 **4. Detectar o Sistema Operacional**

Podemos identificar o sistema operacional usando o pacote `runtime`.

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Println("Sistema Operacional:", runtime.GOOS)
}
```

### 🔹 **Saída esperada no Linux/macOS:**

```
Sistema Operacional: linux
```

### 🔹 **Saída esperada no Windows:**

```
Sistema Operacional: windows
```

---

## 🔹 **5. Obter Diretório do Usuário (`HomeDir`)**

Podemos obter o **diretório home** do usuário atual.

```go
package main

import (
	"fmt"
	"os"
	"os/user"
)

func main() {
	usr, err := user.Current()
	if err != nil {
		fmt.Println("Erro ao obter usuário:", err)
		return
	}

	fmt.Println("Home Directory:", usr.HomeDir)
}
```

### 🔹 **Saída esperada no Linux/macOS:**

```
Home Directory: /home/jean
```

### 🔹 **Saída esperada no Windows:**

```
Home Directory: C:\Users\Jean
```

---

## 🔹 **6. Listar Variáveis de Ambiente**

Podemos listar **todas as variáveis de ambiente** do sistema.

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	envs := os.Environ() // Lista todas as variáveis de ambiente
	for _, env := range envs {
		fmt.Println(env)
	}
}
```

### 🔹 **Saída esperada (exemplo):**

```
PATH=/usr/local/bin:/usr/bin:/bin
HOME=/home/jean
SHELL=/bin/bash
USERNAME=Jean
```

💡 **Podemos buscar variáveis específicas com `os.Getenv("VARIAVEL")`**.

---

## 🔹 **7. Obter o Diretório Atual (`Getwd`)**

Para saber em qual pasta o programa está sendo executado:

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	dir, err := os.Getwd()
	if err != nil {
		fmt.Println("Erro ao obter diretório:", err)
		return
	}

	fmt.Println("Diretório atual:", dir)
}
```

### 🔹 **Saída esperada:**

```
Diretório atual: /home/jean/projetos/go
```

---

## 🔹 **8. Obter o Tempo de Execução (`Uptime`)**

Para saber há quanto tempo o sistema está ligado:

```go
package main

import (
	"fmt"
	"os"
	"time"
)

func main() {
	// Obtendo a hora do sistema
	startTime, err := os.Stat("/proc/1")
	if err != nil {
		fmt.Println("Erro ao obter uptime:", err)
		return
	}

	// Calculando tempo ligado
	uptime := time.Since(startTime.ModTime())
	fmt.Println("Tempo de execução do sistema:", uptime)
}
```

⚠ **Funciona apenas no Linux/macOS!** No Windows, precisaríamos de outro método.

---

O pacote `os` permite **acessar informações do sistema operacional de forma simples e eficiente**. Com ele, podemos:
✅ Obter hostname e nome do usuário  
✅ Identificar o sistema operacional  
✅ Acessar diretórios e variáveis de ambiente  
✅ Descobrir o tempo de execução do sistema
