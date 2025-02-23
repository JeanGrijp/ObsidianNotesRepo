---
tags:
  - Tecnologia
  - GoLang
---
### 🐍 **Cobra: Criando CLIs poderosas no Go** 🚀

O **Cobra** é uma **biblioteca para criar [[CLI]]s (Command Line Interfaces)** no [[Go]] de forma organizada e profissional. Ele é amplamente usado para criar ferramentas de linha de comando robustas, incluindo o próprio **kubectl (CLI do [[Kubernetes]])**.

---

## 🔥 **Por que usar Cobra ao invés de `flag`?**

O pacote `flag` é bom para CLIs simples, mas **Cobra** oferece vantagens como:
✅ **Organização modular** (comandos e subcomandos)  
✅ **Autocompletar (bash, zsh, fish, etc.)**  
✅ **Ajuda automática (`--help`)**  
✅ **Facilidade para criar hierarquia de comandos**  
✅ **Melhor estruturação para CLIs grandes**

---

## 🔧 **Instalação do Cobra**

Para usar o **Cobra**, primeiro instale-o no seu projeto:

```sh
go get -u github.com/spf13/cobra@latest
go get -u github.com/spf13/cobra/cobra@latest
```

---

## 📌 **Exemplo 1: CLI básica com Cobra**

Criaremos um CLI simples que imprime `"Olá, Jean!"`.

### 🔹 Criando o arquivo `main.go`

```go
package main

import (
	"fmt"
	"github.com/spf13/cobra"
)

func main() {
	// Criando o comando root
	var rootCmd = &cobra.Command{
		Use:   "app",
		Short: "Meu CLI com Cobra",
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Println("Olá, Jean!")
		},
	}

	// Executando o comando root
	rootCmd.Execute()
}
```

### 🔹 Como rodar:

```sh
go run main.go
```

### 🔹 Saída:

```
Olá, Jean!
```

🔥 **Já temos uma CLI funcional!**  
E automaticamente, ele já suporta `--help`:

```sh
go run main.go --help
```

### 🔹 Saída:

```
Meu CLI com Cobra

Usage:
  app [flags]

Flags:
  -h, --help   help for app
```

---

## 📌 **Exemplo 2: Adicionando flags**

Agora, vamos permitir que o usuário passe um nome como argumento.

```go
package main

import (
	"fmt"
	"github.com/spf13/cobra"
)

func main() {
	var nome string

	var rootCmd = &cobra.Command{
		Use:   "app",
		Short: "Meu CLI com Cobra",
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Printf("Olá, %s!\n", nome)
		},
	}

	// Adicionando flag --nome
	rootCmd.Flags().StringVarP(&nome, "nome", "n", "Usuário", "Nome da pessoa")

	rootCmd.Execute()
}
```

### 🔹 Como rodar:

```sh
go run main.go --nome=Jean
```

### 🔹 Saída:

```
Olá, Jean!
```

---

## 📌 **Exemplo 3: Criando subcomandos**

Uma das maiores vantagens do **Cobra** é a facilidade para criar **subcomandos**.

Vamos criar uma CLI que tenha um comando `saudar` e um comando `despedir`.

```go
package main

import (
	"fmt"
	"github.com/spf13/cobra"
)

func main() {
	var rootCmd = &cobra.Command{Use: "app"}

	var cmdSaudar = &cobra.Command{
		Use:   "saudar",
		Short: "Sauda um usuário",
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Println("Olá, bem-vindo!")
		},
	}

	var cmdDespedir = &cobra.Command{
		Use:   "despedir",
		Short: "Se despede do usuário",
		Run: func(cmd *cobra.Command, args []string) {
			fmt.Println("Adeus, até a próxima!")
		},
	}

	// Adicionando subcomandos
	rootCmd.AddCommand(cmdSaudar, cmdDespedir)

	rootCmd.Execute()
}
```

### 🔹 Como rodar:

```sh
go run main.go saudar
```

### 🔹 Saída:

```
Olá, bem-vindo!
```

```sh
go run main.go despedir
```

### 🔹 Saída:

```
Adeus, até a próxima!
```

🔥 **Os subcomandos organizam melhor o código e facilitam a expansão da CLI!**

---

## 📌 **Exemplo 4: Autocompletar comandos**

Cobra tem suporte a **autocompletar** em shells como Bash e Zsh.

Para gerar o autocompletar:

```go
var completionCmd = &cobra.Command{
	Use:   "completion",
	Short: "Gera script de autocompletar para o shell",
	Run: func(cmd *cobra.Command, args []string) {
		cmd.Root().GenBashCompletion(os.Stdout)
	},
}
rootCmd.AddCommand(completionCmd)
```

Agora, rodamos:

```sh
go run main.go completion > autocomplete.sh
source autocomplete.sh
```

Agora, ao digitar:

```sh
go run main.go <TAB>
```

Ele sugere os comandos automaticamente. 🚀

---


O **Cobra** é uma **ferramenta poderosa para criar CLIs no Go**!  
Ele facilita a organização, modularização e adição de recursos como: ✅ Subcomandos  
✅ Flags  
✅ Autocompletar  
✅ Ajuda automática

Se você precisar criar **CLIs profissionais**, Cobra é a escolha certa. 💪🔥

Bora um exemplo mais avançado, tipo um CLI que chama uma API? 😃

### 🚀 **Criando um CLI que consome uma API com Cobra** 🐍

Vamos construir um **CLI em Go usando Cobra** que faz requisições HTTP para uma API pública e exibe os dados no terminal.

---

## 📌 **O que vamos fazer?**

Criar um CLI chamado `app` que:

1. **Busca informações sobre um usuário do GitHub** através da API do GitHub.
2. **Permite buscar repositórios públicos do usuário**.
3. **Permite formatar a saída como JSON ou texto**.

---

## 🔧 **Passo 1: Instalar Dependências**

Antes de começar, instale o Cobra:

```sh
go get -u github.com/spf13/cobra@latest
go get -u github.com/spf13/cobra/cobra@latest
```

Também precisaremos do pacote `net/http` e `encoding/json`, que já fazem parte da biblioteca padrão do Go.

---

## 📜 **Código do CLI com Cobra**

```go
package main

import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"net/http"
	"os"

	"github.com/spf13/cobra"
)

// Estrutura para armazenar os dados do usuário do GitHub
type GitHubUser struct {
	Login     string `json:"login"`
	Name      string `json:"name"`
	Followers int    `json:"followers"`
	Following int    `json:"following"`
	PublicRepos int  `json:"public_repos"`
}

// Estrutura para armazenar os dados dos repositórios
type GitHubRepo struct {
	Name string `json:"name"`
	URL  string `json:"html_url"`
}

func main() {
	var outputFormat string

	// Comando principal
	var rootCmd = &cobra.Command{
		Use:   "app",
		Short: "CLI para buscar informações do GitHub",
	}

	// Comando para buscar usuário
	var userCmd = &cobra.Command{
		Use:   "user [username]",
		Short: "Busca informações sobre um usuário do GitHub",
		Args:  cobra.ExactArgs(1),
		Run: func(cmd *cobra.Command, args []string) {
			username := args[0]
			user, err := fetchGitHubUser(username)
			if err != nil {
				fmt.Println("Erro ao buscar usuário:", err)
				return
			}

			if outputFormat == "json" {
				printJSON(user)
			} else {
				fmt.Printf("Nome: %s\nUsuário: %s\nSeguidores: %d\nSeguindo: %d\nRepositórios Públicos: %d\n",
					user.Name, user.Login, user.Followers, user.Following, user.PublicRepos)
			}
		},
	}

	// Comando para buscar repositórios
	var reposCmd = &cobra.Command{
		Use:   "repos [username]",
		Short: "Lista os repositórios públicos de um usuário",
		Args:  cobra.ExactArgs(1),
		Run: func(cmd *cobra.Command, args []string) {
			username := args[0]
			repos, err := fetchGitHubRepos(username)
			if err != nil {
				fmt.Println("Erro ao buscar repositórios:", err)
				return
			}

			if outputFormat == "json" {
				printJSON(repos)
			} else {
				fmt.Println("Repositórios Públicos:")
				for _, repo := range repos {
					fmt.Printf("- %s (%s)\n", repo.Name, repo.URL)
				}
			}
		},
	}

	// Adiciona a flag de formato de saída
	userCmd.Flags().StringVarP(&outputFormat, "output", "o", "text", "Formato da saída (text/json)")
	reposCmd.Flags().StringVarP(&outputFormat, "output", "o", "text", "Formato da saída (text/json)")

	// Adiciona os comandos ao CLI
	rootCmd.AddCommand(userCmd, reposCmd)

	// Executa o CLI
	rootCmd.Execute()
}

// Função para buscar informações do usuário do GitHub
func fetchGitHubUser(username string) (GitHubUser, error) {
	url := fmt.Sprintf("https://api.github.com/users/%s", username)
	resp, err := http.Get(url)
	if err != nil {
		return GitHubUser{}, err
	}
	defer resp.Body.Close()

	if resp.StatusCode != http.StatusOK {
		return GitHubUser{}, fmt.Errorf("usuário não encontrado")
	}

	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		return GitHubUser{}, err
	}

	var user GitHubUser
	if err := json.Unmarshal(body, &user); err != nil {
		return GitHubUser{}, err
	}

	return user, nil
}

// Função para buscar repositórios do GitHub
func fetchGitHubRepos(username string) ([]GitHubRepo, error) {
	url := fmt.Sprintf("https://api.github.com/users/%s/repos", username)
	resp, err := http.Get(url)
	if err != nil {
		return nil, err
	}
	defer resp.Body.Close()

	if resp.StatusCode != http.StatusOK {
		return nil, fmt.Errorf("usuário não encontrado")
	}

	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		return nil, err
	}

	var repos []GitHubRepo
	if err := json.Unmarshal(body, &repos); err != nil {
		return nil, err
	}

	return repos, nil
}

// Função auxiliar para imprimir JSON formatado
func printJSON(data interface{}) {
	result, err := json.MarshalIndent(data, "", "  ")
	if err != nil {
		fmt.Println("Erro ao formatar JSON:", err)
		return
	}
	fmt.Println(string(result))
}
```

---

## 🎯 **Como usar o CLI?**

### 🔹 **Buscar informações de um usuário**

```sh
go run main.go user JeanGrijp
```

🔹 **Saída:**

```
Nome: Jean Grijp
Usuário: JeanGrijp
Seguidores: 200
Seguindo: 150
Repositórios Públicos: 50
```

Ou em **JSON**:

```sh
go run main.go user JeanGrijp --output=json
```

🔹 **Saída JSON formatada:**

```json
{
  "login": "JeanGrijp",
  "name": "Jean Grijp",
  "followers": 200,
  "following": 150,
  "public_repos": 50
}
```

---

### 🔹 **Buscar repositórios de um usuário**

```sh
go run main.go repos JeanGrijp
```

🔹 **Saída:**

```
Repositórios Públicos:
- meu-projeto (https://github.com/JeanGrijp/meu-projeto)
- app-teste (https://github.com/JeanGrijp/app-teste)
```

Ou em JSON:

```sh
go run main.go repos JeanGrijp --output=json
```

🔹 **Saída JSON formatada:**

```json
[
  {
    "name": "meu-projeto",
    "url": "https://github.com/JeanGrijp/meu-projeto"
  },
  {
    "name": "app-teste",
    "url": "https://github.com/JeanGrijp/app-teste"
  }
]
```

---

## 🚀 **O que aprendemos?**

✅ Criamos um **CLI modular** com o **Cobra**  
✅ Fizemos **requisições HTTP** para uma API pública  
✅ Usamos **flags para personalizar a saída** (texto ou JSON)  
✅ Criamos **comandos e subcomandos** (`user` e `repos`)

Este é um **exemplo prático** de como criar uma ferramenta de linha de comando útil! 💪🔥

Quer adicionar autenticação via **GitHub Token** ou salvar os dados em um arquivo? 😃