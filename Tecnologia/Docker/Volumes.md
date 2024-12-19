---
tags:
  - Tecnologia
  - Docker
---
Os **volumes** no Docker [[Compose]] são usados para armazenar dados de forma persistente, garantindo que as informações criadas dentro de um [[container]] não sejam perdidas mesmo se o container for reiniciado ou removido.

Quando você cria um volume, ele mapeia um local do sistema de arquivos do container para o host. Isso significa que os dados armazenados nessa localização persistem no host e podem ser acessados novamente quando o container é recriado.

No arquivo `docker-compose.yml`, você pode definir volumes diretamente na configuração do serviço. Por exemplo:

```yaml
version: '3.8'

services:
  database:
    image: postgres:14
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

Nesse exemplo, o volume `db-data` é usado para armazenar os dados do banco de dados [[PostgreSQL]]. Esses dados ficam salvos no host e continuam disponíveis mesmo que o container do PostgreSQL seja removido e recriado.

Além disso, você também pode mapear diretórios específicos do host, permitindo que dados sejam armazenados em um caminho que você escolha. Por exemplo:

```yaml
version: '3.8'

services:
  database:
    image: postgres:14
    volumes:
      - ./data:/var/lib/postgresql/data
```

Aqui, o diretório `./data` do host será usado para armazenar os dados do banco. Isso permite que você visualize ou faça backups diretamente dos arquivos no seu sistema.

Os volumes são úteis para manter os dados intactos durante atualizações ou quando você precisa compartilhar informações entre múltiplos containers. Eles tornam o uso do Docker mais confiável para aplicações que exigem persistência de dados, como bancos de dados ou sistemas de arquivos.

## Conectando uma aplicação Go ao banco de dados

Um exemplo de como configurar um `docker-compose.yml` para uma aplicação [[Go]] e um banco de dados [[PostgreSQL]], onde a aplicação se conecta ao banco:

### Estrutura do Projeto

```plaintext
.
├── docker-compose.yml
├── go-app/
│   ├── Dockerfile
│   ├── main.go
```

### Arquivo `main.go` (Aplicação Go)

Um exemplo básico de código Go que se conecta ao PostgreSQL usando a biblioteca `github.com/lib/pq`:

```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/lib/pq"
)

func main() {
	// Configuração da conexão
	connStr := "host=database user=user password=password dbname=mydb sslmode=disable"
	db, err := sql.Open("postgres", connStr)
	if err != nil {
		log.Fatalf("Erro ao conectar ao banco: %v", err)
	}
	defer db.Close()

	err = db.Ping()
	if err != nil {
		log.Fatalf("Erro ao verificar a conexão: %v", err)
	}

	fmt.Println("Conexão com o banco de dados bem-sucedida!")
}
```

### Arquivo `Dockerfile` para a Aplicação Go

Crie um **[[Dockerfile]]** no diretório `go-app` para compilar e executar a aplicação:

```dockerfile
# Imagem base
FROM golang:1.20

# Diretório de trabalho dentro do container
WORKDIR /app

# Copiar arquivos para o container
COPY . .

# Baixar dependências e compilar o aplicativo
RUN go mod init go-app && go mod tidy && go build -o main .

# Comando para executar o aplicativo
CMD ["./main"]
```

### Arquivo `docker-compose.yml`

Aqui está o `docker-compose.yml` que configura os dois serviços (Go e PostgreSQL):

```yaml
version: '3.8'

services:
  app:
    build:
      context: ./go-app
    depends_on:
      - database
    environment:
      - DB_HOST=database
      - DB_USER=user
      - DB_PASSWORD=password
      - DB_NAME=mydb

  database:
    image: postgres:14
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

### O Que Está Acontecendo?

1. **Serviço `app` (Aplicação Go)**:
    - É construído usando o Dockerfile no diretório `go-app`.
    - Depende do serviço `database`, ou seja, o banco será inicializado antes da aplicação Go.
    - As variáveis de ambiente fornecem as credenciais e a configuração para a conexão ao banco.
2. **Serviço `database` (PostgreSQL)**:
    - Usa a imagem oficial do PostgreSQL.
    - Expõe a porta `5432` no host para acessar o banco externamente, se necessário.
    - Cria um volume chamado `db-data` para persistir os dados.
3. **Volumes**:
    - O volume `db-data` garante que os dados do PostgreSQL sejam armazenados no host, evitando perda de dados ao reiniciar o container.

### Comandos Para Rodar

1. **Iniciar os serviços**:

    ```bash
docker-compose up --build
```

2. **Verificar os logs**:

    ```bash
docker-compose logs -f
```

3. **Parar e limpar os serviços**:

    ```bash
docker-compose down
```


### Testando a Conexão

Após rodar o `docker-compose`, você deve ver no terminal da aplicação Go a mensagem de "Conexão com o banco de dados bem-sucedida!". Isso confirma que a aplicação Go conseguiu se conectar ao PostgreSQL. Essa configuração cria um ambiente isolado e replicável para desenvolver e testar sua aplicação.