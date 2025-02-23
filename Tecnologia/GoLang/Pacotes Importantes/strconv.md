---
tags:
  - GoLang
  - Tecnologia
---
### 🔢 **`strconv` – Convertendo Tipos de Dados em Go** 🚀

O pacote **`strconv`** no Go é usado para converter **strings em números**, **números em strings** e manipular outros tipos de conversões, como booleanos. Ele é essencial para lidar com entrada de dados e processamento numérico.

---

## 🔹 **1. Convertendo String para Inteiro (`strconv.Atoi`)**

A função `strconv.Atoi()` converte uma **string numérica** para um **inteiro** (`int`).

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	numStr := "42"

	num, err := strconv.Atoi(numStr) // Converte string para int
	if err != nil {
		fmt.Println("Erro ao converter:", err)
		return
	}

	fmt.Println("Número inteiro:", num) // Saída: 42
}
```

✅ **Retorna um `int` e um erro (`err`)** caso a string não seja um número válido.

### ⚠ **E se a string não for um número?**

```go
num, err := strconv.Atoi("abc")
fmt.Println(num, err) // Saída: 0 strconv.Atoi: parsing "abc": invalid syntax
```

Se a string **não puder ser convertida**, **retorna `0` e um erro**.

---

## 🔹 **2. Convertendo Inteiro para String (`strconv.Itoa`)**

A função `strconv.Itoa()` converte um **inteiro (`int`) para uma string**.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	num := 99
	str := strconv.Itoa(num) // Converte int para string
	fmt.Println("Número em string:", str) // Saída: "99"
}
```

---

## 🔹 **3. Convertendo String para Float (`strconv.ParseFloat`)**

Para converter **strings para `float64`**, usamos `strconv.ParseFloat()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	numStr := "3.1415"

	num, err := strconv.ParseFloat(numStr, 64) // 64 = precisão de bits
	if err != nil {
		fmt.Println("Erro ao converter:", err)
		return
	}

	fmt.Println("Número float:", num) // Saída: 3.1415
}
```

✅ **O segundo argumento (`64`) define a precisão do float**.

### ⚠ **E se a string for inválida?**

```go
num, err := strconv.ParseFloat("abc", 64)
fmt.Println(num, err) // Saída: 0 strconv.ParseFloat: parsing "abc": invalid syntax
```

Se a string **não for um número válido**, retorna `0` e um erro.

---

## 🔹 **4. Convertendo Float para String (`strconv.FormatFloat`)**

Para converter um `float64` para `string`, usamos `strconv.FormatFloat()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	num := 3.1415
	str := strconv.FormatFloat(num, 'f', 2, 64) // 2 casas decimais
	fmt.Println("Número em string:", str) // Saída: "3.14"
}
```

🔹 **Parâmetros do `FormatFloat`**:

- `'f'` → Formato decimal fixo (exemplo: `123.45`)
- `2` → Número de casas decimais
- `64` → Precisão do número

---

## 🔹 **5. Convertendo String para Boolean (`strconv.ParseBool`)**

Para converter **strings para booleanos**, usamos `strconv.ParseBool()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	valStr := "true"

	val, err := strconv.ParseBool(valStr)
	if err != nil {
		fmt.Println("Erro ao converter:", err)
		return
	}

	fmt.Println("Boolean:", val) // Saída: true
}
```

### 🔹 **Valores aceitos**:

✅ `"true"`, `"1"` → **Retorna `true`**  
❌ `"false"`, `"0"` → **Retorna `false`**

### ⚠ **Valores inválidos retornam erro**:

```go
val, err := strconv.ParseBool("abc")
fmt.Println(val, err) // Saída: false strconv.ParseBool: parsing "abc": invalid syntax
```

---

## 🔹 **6. Convertendo Boolean para String (`strconv.FormatBool`)**

Para converter um **booleano para string**, usamos `strconv.FormatBool()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	b := true
	str := strconv.FormatBool(b)
	fmt.Println("Boolean em string:", str) // Saída: "true"
}
```

---

## 🔹 **7. Convertendo String para Base Numérica (`strconv.ParseInt`)**

Se precisar converter uma string para um **inteiro em bases diferentes** (`binário`, `octal`, `hexadecimal`), use `strconv.ParseInt()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	// Base 10 (Decimal)
	decimal, _ := strconv.ParseInt("42", 10, 64)
	fmt.Println("Decimal:", decimal) // Saída: 42

	// Base 2 (Binário)
	binario, _ := strconv.ParseInt("101010", 2, 64)
	fmt.Println("Binário:", binario) // Saída: 42

	// Base 16 (Hexadecimal)
	hexadecimal, _ := strconv.ParseInt("2A", 16, 64)
	fmt.Println("Hexadecimal:", hexadecimal) // Saída: 42
}
```

### 📝 **Parâmetros do `strconv.ParseInt()`**:

- Primeiro argumento → **String numérica**
- Segundo argumento → **Base (`2` para binário, `10` para decimal, `16` para hexadecimal, etc.)**
- Terceiro argumento → **Precisão (32 ou 64 bits)**

---

## 🔹 **8. Convertendo Número para Base Numérica (`strconv.FormatInt`)**

Se precisar converter um número para **binário, octal ou hexadecimal**, use `strconv.FormatInt()`.

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	num := int64(42)

	fmt.Println("Decimal:", strconv.FormatInt(num, 10))  // "42"
	fmt.Println("Binário:", strconv.FormatInt(num, 2))   // "101010"
	fmt.Println("Hexadecimal:", strconv.FormatInt(num, 16)) // "2a"
}
```

---

O pacote `strconv` facilita **conversões entre tipos** no Go:

|Função|Conversão|Exemplo de Entrada|Exemplo de Saída|
|---|---|---|---|
|`strconv.Atoi()`|String → Int|`"42"`|`42`|
|`strconv.Itoa()`|Int → String|`42`|`"42"`|
|`strconv.ParseFloat()`|String → Float64|`"3.14"`|`3.14`|
|`strconv.FormatFloat()`|Float64 → String|`3.14`|`"3.14"`|
|`strconv.ParseBool()`|String → Boolean|`"true"`|`true`|
|`strconv.FormatBool()`|Boolean → String|`true`|`"true"`|
|`strconv.ParseInt()`|String → Int (base N)|`"1010", 2, 64`|`10`|
|`strconv.FormatInt()`|Int → String (base N)|`42, 16`|`"2a"`|


# 🔹 **Vamos vê um exemplo de uso prático em um projeto real** 😃


### 🚀 **Projeto Prático: CLI de Conversão Numérica com `strconv` e `Cobra`** 🐍

Vamos criar um **[[CLI]] (Command Line Interface)** que permite ao usuário converter números entre **decimal, binário e hexadecimal** usando `strconv` e a biblioteca [[Cobra]]. Esse é um projeto útil para quem trabalha com programação de sistemas, redes e eletrônica digital.

---

## 📌 **O que o CLI faz?**

O usuário pode:

1. Converter um **número decimal para binário ou hexadecimal**
2. Converter um **número binário ou hexadecimal para decimal**
3. Escolher o formato de saída (padrão é texto, mas pode ser JSON)

---

## 🔧 **Passo 1: Instalar Dependências**

Antes de começar, instale o **Cobra**:

```sh
go get -u github.com/spf13/cobra@latest
go get -u github.com/spf13/cobra/cobra@latest
```

---

## 📜 **Código Completo do CLI**

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
	"strconv"

	"github.com/spf13/cobra"
)

// Estrutura para saída JSON
type Result struct {
	Input    string `json:"input"`
	BaseFrom string `json:"base_from"`
	BaseTo   string `json:"base_to"`
	Result   string `json:"result"`
}

func main() {
	var outputFormat string

	// Criando comando raiz
	var rootCmd = &cobra.Command{
		Use:   "convert",
		Short: "Converte números entre decimal, binário e hexadecimal",
	}

	// Comando para converter Decimal → Binário/Hexadecimal
	var decCmd = &cobra.Command{
		Use:   "dec [número]",
		Short: "Converte um número decimal para binário ou hexadecimal",
		Args:  cobra.ExactArgs(1),
		Run: func(cmd *cobra.Command, args []string) {
			num, err := strconv.ParseInt(args[0], 10, 64)
			if err != nil {
				fmt.Println("Erro: Número inválido")
				return
			}

			result := Result{
				Input:    args[0],
				BaseFrom: "Decimal",
				BaseTo:   "Binário e Hexadecimal",
				Result:   fmt.Sprintf("Binário: %s, Hexadecimal: %s", strconv.FormatInt(num, 2), strconv.FormatInt(num, 16)),
			}

			printOutput(result, outputFormat)
		},
	}

	// Comando para converter Binário → Decimal
	var binCmd = &cobra.Command{
		Use:   "bin [número]",
		Short: "Converte um número binário para decimal",
		Args:  cobra.ExactArgs(1),
		Run: func(cmd *cobra.Command, args []string) {
			num, err := strconv.ParseInt(args[0], 2, 64)
			if err != nil {
				fmt.Println("Erro: Número binário inválido")
				return
			}

			result := Result{
				Input:    args[0],
				BaseFrom: "Binário",
				BaseTo:   "Decimal",
				Result:   fmt.Sprintf("Decimal: %d", num),
			}

			printOutput(result, outputFormat)
		},
	}

	// Comando para converter Hexadecimal → Decimal
	var hexCmd = &cobra.Command{
		Use:   "hex [número]",
		Short: "Converte um número hexadecimal para decimal",
		Args:  cobra.ExactArgs(1),
		Run: func(cmd *cobra.Command, args []string) {
			num, err := strconv.ParseInt(args[0], 16, 64)
			if err != nil {
				fmt.Println("Erro: Número hexadecimal inválido")
				return
			}

			result := Result{
				Input:    args[0],
				BaseFrom: "Hexadecimal",
				BaseTo:   "Decimal",
				Result:   fmt.Sprintf("Decimal: %d", num),
			}

			printOutput(result, outputFormat)
		},
	}

	// Adicionando flag para escolher formato de saída
	decCmd.Flags().StringVarP(&outputFormat, "output", "o", "text", "Formato da saída (text/json)")
	binCmd.Flags().StringVarP(&outputFormat, "output", "o", "text", "Formato da saída (text/json)")
	hexCmd.Flags().StringVarP(&outputFormat, "output", "o", "text", "Formato da saída (text/json)")

	// Adicionando os comandos ao CLI
	rootCmd.AddCommand(decCmd, binCmd, hexCmd)

	// Executando CLI
	rootCmd.Execute()
}

// Função auxiliar para imprimir saída
func printOutput(result Result, format string) {
	if format == "json" {
		jsonOutput, _ := json.MarshalIndent(result, "", "  ")
		fmt.Println(string(jsonOutput))
	} else {
		fmt.Println(result.Result)
	}
}
```

---

## 🎯 **Como usar o CLI?**

### 🔹 **Converter Decimal para Binário e Hexadecimal**

```sh
go run main.go dec 42
```

🔹 **Saída:**

```
Binário: 101010, Hexadecimal: 2a
```

Ou **saída em JSON**:

```sh
go run main.go dec 42 --output=json
```

🔹 **Saída JSON:**

```json
{
  "input": "42",
  "base_from": "Decimal",
  "base_to": "Binário e Hexadecimal",
  "result": "Binário: 101010, Hexadecimal: 2a"
}
```

---

### 🔹 **Converter Binário para Decimal**

```sh
go run main.go bin 101010
```

🔹 **Saída:**

```
Decimal: 42
```

---

### 🔹 **Converter Hexadecimal para Decimal**

```sh
go run main.go hex 2a
```

🔹 **Saída:**

```
Decimal: 42
```

---

## 🚀 **O que aprendemos?**

✅ Criamos um **CLI organizado e modular** usando `Cobra`  
✅ Convertendo números entre **decimal, binário e hexadecimal**  
✅ Manipulamos strings e números com `strconv`  
✅ Adicionamos suporte a **JSON e saída formatada**