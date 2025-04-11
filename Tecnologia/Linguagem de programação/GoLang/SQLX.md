---
tags:
  - Tecnologia
  - GoLang
  - Database
---
## 🎓 Aula: **Introdução ao `sqlx` com Go**

### 📌 O que é `sqlx`?

`sqlx` é uma biblioteca para [[Go]] que **estende o pacote `database/sql`**, oferecendo:

- Binds automáticos entre [[queries]] e [[Structs]]
- Suporte a queries nomeadas
- Execução mais simples e segura de SELECT, INSERT, UPDATE
- Tratamento elegante de dados nulos (`NULL`)

---

## 1️⃣ Instalação

```bash
go get github.com/jmoiron/sqlx
```

E importe no código:

```go
import "github.com/jmoiron/sqlx"
```

---

## 2️⃣ Conectando ao Banco de Dados

```go
import (
	"github.com/jmoiron/sqlx"
	_ "github.com/lib/pq" // driver PostgreSQL
)

func ConnectDB() (*sqlx.DB, error) {
	dsn := "host=localhost user=postgres password=123 dbname=meubanco port=5432 sslmode=disable"
	db, err := sqlx.Connect("postgres", dsn)
	if err != nil {
		return nil, err
	}
	return db, nil
}
```

---

## 3️⃣ Criando Structs

```go
type User struct {
	ID    int    `db:"id"`
	Name  string `db:"name"`
	Email string `db:"email"`
}
```

---

## 4️⃣ SELECT usando `Get` e `Select`

```go
// Buscar um único registro
var user User
err := db.Get(&user, "SELECT * FROM users WHERE id=$1", 1)

// Buscar vários registros
var users []User
err := db.Select(&users, "SELECT * FROM users")
```

---

## 5️⃣ INSERT com NamedQuery

```go
user := User{Name: "Jean", Email: "jean@email.com"}
query := `
  INSERT INTO users (name, email)
  VALUES (:name, :email)
  RETURNING id
`
stmt, err := db.NamedQuery(query, user)
if stmt.Next() {
	stmt.Scan(&user.ID)
}
```

---

## 6️⃣ UPDATE ou DELETE

```go
_, err := db.Exec("UPDATE users SET name=$1 WHERE id=$2", "João", 1)
```

---

## 7️⃣ Tratando `NULL` com ponteiros

Use ponteiros em campos opcionais:

```go
type Product struct {
	ID          int     `db:"id"`
	Name        string  `db:"name"`
	Description *string `db:"description"` // pode ser NULL
}
```

---

## 8️⃣ Dica de Ouro: Use `Context`

Sempre use `context.Context` com tempo limite:

```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()

var user User
err := db.GetContext(ctx, &user, "SELECT * FROM users WHERE id=$1", 1)
```

---
# 🔹 **O que são transações?**

Transações garantem que **múltiplas operações no banco sejam executadas como uma unidade atômica**. Se algo falhar, **tudo é revertido** (`ROLLBACK`). Se der certo, confirmamos (`COMMIT`).

### **🛠 Exemplo sem transação:**

Imagine que você precisa:

1. Criar um `Tutor`.
2. Criar um `Pet` associado a esse `Tutor`.

Se a criação do pet falhar, o tutor já terá sido inserido no banco, **deixando dados inconsistentes**. 🤯

Para evitar isso, usamos transações!

---

## 🏗 **Criando uma transação com `sqlx`**

### ✅ **Exemplo de transação para inserir um `Tutor` e um `Pet` juntos:**

```go
func (r *PetRepository) CreateWithTutor(tutor models.Tutor, pet models.Pet) error {
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()

	tx, err := r.DB.BeginTxx(ctx, nil) // Inicia a transação
	if err != nil {
		slog.ErrorContext(ctx, "Failed to start transaction", slog.Any("error", err))
		return err
	}

	// 1️⃣ Insere o Tutor
	queryTutor := `INSERT INTO Tutor (name, email) VALUES (:name, :email) RETURNING id`
	rows, err := tx.NamedQueryContext(ctx, queryTutor, tutor)
	if err != nil {
		tx.Rollback() // ❌ Reverte tudo se falhar
		slog.ErrorContext(ctx, "Failed to insert tutor", slog.Any("error", err))
		return err
	}
	if rows.Next() {
		rows.Scan(&tutor.ID)
	}
	rows.Close()

	// 2️⃣ Insere o Pet vinculado ao Tutor
	pet.TutorID = tutor.ID
	queryPet := `INSERT INTO Pet (name, species, tutor_id) VALUES (:name, :species, :tutor_id)`
	_, err = tx.NamedExecContext(ctx, queryPet, pet)
	if err != nil {
		tx.Rollback() // ❌ Reverte tudo se falhar
		slog.ErrorContext(ctx, "Failed to insert pet", slog.Any("error", err))
		return err
	}

	// 3️⃣ Se tudo deu certo, confirma as operações!
	err = tx.Commit()
	if err != nil {
		slog.ErrorContext(ctx, "Failed to commit transaction", slog.Any("error", err))
		return err
	}

	slog.InfoContext(ctx, "Tutor and Pet inserted successfully", slog.Int("tutor_id", tutor.ID))
	return nil
}
```

---

## 📝 **Explicação**

1. **Criamos a transação com `tx, err := r.DB.BeginTxx(ctx, nil)`**.
2. **Executamos a inserção do `Tutor`** e pegamos o `id`.
3. **Inserimos o `Pet` associado ao `Tutor`**.
4. **Se algo falhar, chamamos `tx.Rollback()`** e nada é salvo no banco.
5. **Se tudo for bem, chamamos `tx.Commit()`** para confirmar.

---

## 🚀 **Bônus: Executando múltiplas queries com `Tx.Exec()`**

Caso precise executar **várias queries sem NamedQuery**, use:

```go
_, err := tx.Exec("UPDATE pets SET name = $1 WHERE id = $2", "Thor", 1)
```

---

## 📌 **Conclusão**

Agora você aprendeu a: ✔ Criar transações no `sqlx`.  
✔ Garantir **consistência** no banco de dados.  
✔ Evitar cadastros incompletos com `ROLLBACK`.

---

# 🔹 **O que são JOINs?**

JOINs são usados para combinar dados de **múltiplas tabelas** com base em uma relação entre elas.

No `sqlx`, o objetivo é:

- **Evitar múltiplas queries** quando precisamos de informações de diferentes tabelas.
- **Melhorar performance** ao reduzir idas e voltas ao banco.
- **Facilitar o uso de [[Structs]] aninhadas** em Go.

---

## 🔎 **Tipos de JOINs**

1️⃣ **INNER JOIN** → Retorna apenas registros que possuem correspondência em ambas as tabelas.  
2️⃣ **LEFT JOIN** → Retorna todos os registros da tabela da esquerda e os correspondentes da direita (se houver).  
3️⃣ **RIGHT JOIN** → Retorna todos os registros da direita e os correspondentes da esquerda.  
4️⃣ **FULL JOIN** → Retorna todos os registros quando há correspondência em qualquer uma das tabelas.

---

## ✅ **Exemplo Prático: Buscar Pets com os dados do Tutor**

### 🎯 **Problema**

Queremos obter todos os pets **com as informações do tutor**, usando um **INNER JOIN** entre as tabelas `Pet` e `Tutor`.

### **📌 Modelos**

```go
type Tutor struct {
	ID    int    `db:"id"`
	Name  string `db:"name"`
	Email string `db:"email"`
}

type Pet struct {
	ID      int     `db:"id"`
	Name    string  `db:"name"`
	Species string  `db:"species"`
	TutorID int     `db:"tutor_id"`
	Tutor   *Tutor  `db:"tutor"` // Relacionamento 1:N
}
```

---

### **🛠 Query com `INNER JOIN` no `sqlx`**

```go
func (r *PetRepository) GetAllWithTutors() ([]models.Pet, error) {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	// 🔹 Query com INNER JOIN
	query := `
		SELECT p.id, p.name, p.species, p.tutor_id, 
		       t.id AS "tutor.id", t.name AS "tutor.name", t.email AS "tutor.email"
		FROM pet p
		INNER JOIN tutor t ON p.tutor_id = t.id
	`

	var pets []models.Pet
	err := r.DB.SelectContext(ctx, &pets, query)
	if err != nil {
		slog.ErrorContext(ctx, "Error fetching pets with tutors", slog.Any("error", err))
		return nil, err
	}

	slog.InfoContext(ctx, "Pets with tutors retrieved successfully", slog.Int("count", len(pets)))
	return pets, nil
}
```

---

## 🔎 **Explicação**

1️⃣ **Fazemos um `INNER JOIN` entre `Pet` e `Tutor`** com a condição `p.tutor_id = t.id`.  
2️⃣ **Usamos alias (`AS`)** para mapear os campos do tutor diretamente dentro do struct `Pet.Tutor`.  
3️⃣ **O `sqlx` preenche automaticamente os dados do tutor** na estrutura do `Pet`.  
4️⃣ **Se o tutor não existir, não será retornado (porque usamos INNER JOIN).**

---

## 🚀 **Exemplo com `LEFT JOIN` (Tutor opcional)**

Se um pet puder existir **sem tutor**, use **LEFT JOIN**:

```go
query := `
	SELECT p.id, p.name, p.species, p.tutor_id, 
	       COALESCE(t.id, 0) AS "tutor.id", 
	       COALESCE(t.name, '') AS "tutor.name", 
	       COALESCE(t.email, '') AS "tutor.email"
	FROM pet p
	LEFT JOIN tutor t ON p.tutor_id = t.id
`
```

🔹 `COALESCE()` define valores padrão caso `NULL` seja retornado.  
🔹 Isso impede que o `sqlx` retorne erro ao tentar mapear valores `NULL`.

---

## 📌 **Conclusão**

Agora você aprendeu a: ✔ Usar `INNER JOIN` para buscar informações de múltiplas tabelas.  
✔ Mapear **structs aninhadas** no `sqlx`.  
✔ Evitar erros com `COALESCE()` em `LEFT JOIN`.

---

# 🔹 **O que é Paginação?**

Paginação é uma técnica usada para **dividir grandes conjuntos de dados** em partes menores. Isso melhora:

- ✅ **Performance** → evita sobrecarregar o banco e a API.
- ✅ **Usabilidade** → facilita a navegação entre páginas de resultados.
- ✅ **Escalabilidade** → reduz consumo de memória e CPU.

---

## 📌 **Estratégia com `LIMIT` e `OFFSET`**

A abordagem mais comum para paginação é usar **`LIMIT` e `OFFSET`** no SQL:

```sql
SELECT * FROM pet
ORDER BY id ASC
LIMIT 10 OFFSET 20;
```

🔹 **`LIMIT 10`** → Retorna 10 registros.  
🔹 **`OFFSET 20`** → Pula os primeiros 20 registros.

**Fórmula para `OFFSET`:**

$$
\text{OFFSET} = (\text{página} - 1) \times \text{tamanho\_da\_página}
$$

é usada para **paginação** em bancos de dados, como PostgreSQL e MySQL, quando você precisa buscar resultados de forma paginada. Vamos quebrar isso:

### 📌 **O que significa cada parte?**

- **OFFSET** → Determina quantos registros serão "pulados" antes de começar a retornar os resultados.
- **página** → O número da página que você quer buscar.
- **tamanho_da_página** → A quantidade de registros que cada página contém.

### 📖 **Exemplo na prática**

Suponha que temos uma tabela `pets` com 100 registros e queremos exibir **10 registros por página**.

| Página | Cálculo do OFFSET | Registros retornados     |
| ------ | ----------------- | ------------------------ |
| 1      | $$(1−1)×10= 0$$   | Registros de **1 a 10**  |
| 2      | $$(2−1)×10= 10$$  | Registros de **11 a 20** |
| 3      | $$(3−1)×10 = 20$$ | Registros de **21 a 30** |
| 4      | $$(4−1)×10 = 30$$ | Registros de **31 a 40** |

### 🔥 **Como aplicar no SQL**

Se quisermos buscar a **página 2**, onde cada página tem **10 registros**, usamos:

```sql
SELECT * FROM pets
LIMIT 10 OFFSET 10;
```

Se quisermos a **página 3**, usamos:

```sql
SELECT * FROM pets
LIMIT 10 OFFSET 20;
```

### 🚀 **Resumo**

- **OFFSET** determina quantos registros devem ser ignorados.
- Quanto maior a página, mais registros ele pula antes de começar a retornar.
- **LIMIT** define quantos registros serão retornados por página.

---

## 🛠 **Implementação no `sqlx`**

Vamos criar um método para **buscar pets paginados**, onde o usuário pode definir: ✔ **Número da página** (`page`).  
✔ **Tamanho da página** (`pageSize`).

### **📌 Código em Go**

```go
func (r *PetRepository) GetPaginatedPets(page, pageSize int) ([]models.Pet, error) {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	// ⚡ Calculando OFFSET
	offset := (page - 1) * pageSize

	// 🔹 Query com paginação
	query := `SELECT * FROM pet ORDER BY id ASC LIMIT $1 OFFSET $2`

	var pets []models.Pet
	err := r.DB.SelectContext(ctx, &pets, query, pageSize, offset)
	if err != nil {
		slog.ErrorContext(ctx, "Error fetching paginated pets", slog.Any("error", err))
		return nil, err
	}

	slog.InfoContext(ctx, "Paginated pets retrieved successfully", slog.Int("count", len(pets)))
	return pets, nil
}
```

---

## ✅ **Explicação**

1️⃣ **Calculamos o `OFFSET`** com base na página e tamanho da página.  
2️⃣ **A query usa `LIMIT` e `OFFSET`** para limitar os resultados.  
3️⃣ **Usamos `sqlx.SelectContext`** para preencher a `struct` automaticamente.  
4️⃣ **O `slog` loga o número de registros retornados** para debug.

---

## 🚀 **Chamando a função na API**

No handler da API, extraímos `page` e `pageSize` da query string:

```go
func GetPaginatedPets(c *gin.Context) {
	page, _ := strconv.Atoi(c.DefaultQuery("page", "1"))
	pageSize, _ := strconv.Atoi(c.DefaultQuery("pageSize", "10"))

	pets, err := petRepo.GetPaginatedPets(page, pageSize)
	if err != nil {
		c.JSON(http.StatusInternalServerError, gin.H{"error": "Failed to fetch paginated pets"})
		return
	}

	c.JSON(http.StatusOK, pets)
}
```

Agora podemos buscar pets paginados com:

```
GET /api/v1/pets?page=2&pageSize=5
```

---

## 🔥 **Extra: Adicionando Contagem Total**

Para informar ao cliente quantos registros existem no total, podemos modificar a função:

```go
func (r *PetRepository) GetPaginatedPets(page, pageSize int) ([]models.Pet, int, error) {
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	offset := (page - 1) * pageSize

	// 🔹 Contar total de registros
	var total int
	countQuery := "SELECT COUNT(*) FROM pet"
	err := r.DB.GetContext(ctx, &total, countQuery)
	if err != nil {
		slog.ErrorContext(ctx, "Error counting pets", slog.Any("error", err))
		return nil, 0, err
	}

	// 🔹 Buscar pets paginados
	query := `SELECT * FROM pet ORDER BY id ASC LIMIT $1 OFFSET $2`
	var pets []models.Pet
	err = r.DB.SelectContext(ctx, &pets, query, pageSize, offset)
	if err != nil {
		slog.ErrorContext(ctx, "Error fetching paginated pets", slog.Any("error", err))
		return nil, 0, err
	}

	slog.InfoContext(ctx, "Paginated pets retrieved successfully", slog.Int("count", len(pets)))
	return pets, total, nil
}
```

Agora o handler pode retornar a contagem total:

```go
func GetPaginatedPets(c *gin.Context) {
	page, _ := strconv.Atoi(c.DefaultQuery("page", "1"))
	pageSize, _ := strconv.Atoi(c.DefaultQuery("pageSize", "10"))

	pets, total, err := petRepo.GetPaginatedPets(page, pageSize)
	if err != nil {
		c.JSON(http.StatusInternalServerError, gin.H{"error": "Failed to fetch paginated pets"})
		return
	}

	c.JSON(http.StatusOK, gin.H{
		"total": total,
		"data":  pets,
	})
}
```

---

## 🎯 **Resumo**

✔ Paginação evita carregar **muitos registros de uma vez**.  
✔ **`LIMIT` e `OFFSET`** definem o tamanho e a posição dos registros.  
✔ **Incluímos a contagem total** para facilitar a navegação na API.

---
# **Transações** no `sqlx` (BEGIN, COMMIT, ROLLBACK)

## 📌 **O que são transações no banco de dados?**

Uma **transação** agrupa múltiplas operações de banco de dados em uma única unidade de trabalho. Isso significa que **todas as operações devem ser concluídas com sucesso** ou, se ocorrer um erro, elas são **desfeitas** para manter a consistência dos dados.

### 🛠️ **Principais comandos de transações**

1. **`BEGIN`** – Inicia uma transação.
2. **`COMMIT`** – Confirma todas as operações feitas durante a transação.
3. **`ROLLBACK`** – Desfaz todas as operações caso algo dê errado.

---

## 🏗️ **Criando transações no Go com `sqlx`**

No `sqlx`, utilizamos `db.Beginx()` para iniciar uma transação e `tx.Commit()` ou `tx.Rollback()` para confirmá-la ou desfazê-la.

### ✅ **Exemplo de transação simples**

```go
package main

import (
	"fmt"
	"log"

	"github.com/jmoiron/sqlx"
	_ "github.com/lib/pq"
)

var schema = `
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
)`

func main() {
	// Conectando ao banco de dados
	db, err := sqlx.Connect("postgres", "host=localhost user=postgres password=secret dbname=mydb sslmode=disable")
	if err != nil {
		log.Fatal("Error connecting to database:", err)
	}
	defer db.Close()

	// Criando a tabela caso não exista
	db.MustExec(schema)

	// 🔥 Iniciando uma transação
	tx, err := db.Beginx()
	if err != nil {
		log.Fatal("Error starting transaction:", err)
	}

	// 🔹 Inserindo usuário 1
	_, err = tx.Exec("INSERT INTO users (name, email) VALUES ($1, $2)", "Jean", "jean@example.com")
	if err != nil {
		log.Println("Error inserting user 1:", err)
		tx.Rollback() // ❌ Algo deu errado, desfaz a transação
		return
	}

	// 🔹 Inserindo usuário 2 (email duplicado = erro)
	_, err = tx.Exec("INSERT INTO users (name, email) VALUES ($1, $2)", "Pedro", "jean@example.com")
	if err != nil {
		log.Println("Error inserting user 2:", err)
		tx.Rollback() // ❌ Algo deu errado, desfaz a transação
		return
	}

	// 🔥 Se tudo der certo, confirma a transação!
	err = tx.Commit()
	if err != nil {
		log.Fatal("Error committing transaction:", err)
	}

	fmt.Println("✅ Transação concluída com sucesso!")
}
```

---

## 🛑 **O que acontece nesse código?**

1. **Iniciamos uma transação** com `db.Beginx()`.
2. **Tentamos inserir dois usuários** na tabela `users`.
3. Se o **segundo `INSERT` falhar** (por exemplo, devido a um email duplicado), chamamos `tx.Rollback()` para desfazer tudo.
4. Se tudo ocorrer bem, chamamos `tx.Commit()` para confirmar a transação.

---

## 🔄 **Exemplo com `DEFER` para garantir rollback automático**

Podemos usar `defer` para **garantir que um rollback será executado** caso ocorra um erro antes do commit:

```go
func CreateUser(db *sqlx.DB, name string, email string) error {
	tx, err := db.Beginx()
	if err != nil {
		return err
	}

	// Garantindo rollback caso ocorra erro
	defer func() {
		if err != nil {
			tx.Rollback()
		}
	}()

	_, err = tx.Exec("INSERT INTO users (name, email) VALUES ($1, $2)", name, email)
	if err != nil {
		return err
	}

	return tx.Commit() // Confirma a transação
}
```

---

## 🚀 **Resumo**

- **`BEGIN`** inicia uma transação.
- **`COMMIT`** confirma as mudanças no banco.
- **`ROLLBACK`** desfaz as mudanças caso algo dê errado.
- **Use `defer` para garantir que `ROLLBACK` seja chamado automaticamente em caso de erro.**