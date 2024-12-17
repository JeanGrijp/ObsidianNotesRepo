---
tags:
  - Tecnologia
  - Docker
---
O Dockerfile é onde eu vou declarar o que a minha aplicação precisa, é nele que vou escrever a configuração da imagem da aplicação. Um “roteiro” passo a passo, indicando ao [[Docker]] o que deve ser feito para chegar a uma imagem final pronta para uso.

1. **Definição da Base da Imagem**:  
    Normalmente, o Dockerfile começa com uma instrução `FROM`, que indica qual imagem base será utilizada. Por exemplo, você pode iniciar a partir de uma imagem do Ubuntu, do Node.js, do Python, entre outras. Isso estabelece o sistema operacional e/ou ambiente inicial sobre o qual serão aplicadas as próximas instruções.
    
2. **Instalação de Dependências e Ferramentas**:  
    Após definir a imagem base, você pode usar comandos como `RUN` para instalar pacotes, bibliotecas, ou dependências necessárias para a sua aplicação. Por exemplo, se sua aplicação precisa de determinadas bibliotecas do sistema ou um framework específico, você pode incluí-las ali.
    
3. **Cópia de Arquivos da Aplicação**:  
    Instruções como `COPY` ou `ADD` permitem transferir o código-fonte da sua máquina local para dentro da imagem. Dessa forma, quando o container for iniciado, sua aplicação já estará presente.
    
4. **Configuração do Ambiente e Variáveis**:  
    Comandos como `ENV` permitem configurar variáveis de ambiente diretamente na imagem. Assim, parâmetros de configuração não precisam ser ajustados manualmente quando o container for executado.
    
5. **Exposição de Portas e Configurações de Rede**:  
    A instrução `EXPOSE` indica quais portas a aplicação dentro do container vai utilizar, facilitando a comunicação com o ambiente externo.
    
6. **Definição do Ponto de Entrada e Comando Padrão**:  
    O `ENTRYPOINT` e/ou `CMD` definem o que será executado quando o container for iniciado. Isso garante que, ao rodar um `docker run` a partir daquela imagem, o comportamento padrão da aplicação já esteja definido (por exemplo, iniciar um servidor web ou rodar um programa específico).
    

**Resumindo:** O Dockerfile é essencialmente um script que automatiza o processo de construção da sua imagem Docker. Ao invés de manualmente configurar o ambiente todas as vezes, você descreve tudo ali. Com um único comando (`docker build`), o Docker lê as instruções do Dockerfile e gera uma imagem padronizada, pronta para ser usada em qualquer ambiente que suporte Docker, garantindo assim portabilidade, reprodutibilidade e praticidade no desenvolvimento, teste e deploy da sua aplicação.

### exemplo de uso

Um exemplo de um Dockerfile que constrói uma aplicação [[Go]] e prepara um ambiente com [[PostgreSQL]]. É importante ressaltar que, na prática, é comum separar a aplicação do banco de dados em dois [Container](Container.md) distintos (um container para a aplicação e outro para o banco), seguindo o padrão de [[microsserviços]] e aproveitando melhor a infraestrutura do [[Docker]]. Porém, aqui vai um exemplo integrando tudo em uma única [Imagem](Imagens.md) para fins ilustrativos:

```dockerfile
# Etapa 1: Construção do binário da aplicação Go
FROM golang:1.20-alpine AS builder

# Definir diretório de trabalho
WORKDIR /app

# Copiar arquivos de dependências do Go (go.mod e go.sum, se existirem)
COPY go.mod go.sum ./
RUN go mod download

# Copiar o restante do código da aplicação
COPY . .

# Compilar a aplicação Go
RUN go build -o myapp .

# Etapa 2: Preparação da imagem final com PostgreSQL e a aplicação compilada
FROM alpine:3.18

# Instalar PostgreSQL e suas extensões
RUN apk update && apk add --no-cache postgresql postgresql-contrib su-exec bash

# Definir variáveis de ambiente do PostgreSQL
ENV PGDATA=/var/lib/postgresql/data
RUN mkdir -p $PGDATA && chown postgres:postgres $PGDATA

# Copiar o binário da aplicação gerado na etapa anterior
COPY --from=builder /app/myapp /usr/local/bin/myapp

# Trocar para o usuário postgres (para iniciar o banco de dados de maneira mais segura)
USER postgres

# Inicializar o cluster do PostgreSQL
RUN initdb -D $PGDATA

# Configurar para que o banco aceite conexões (ajustes de configuração podem ser feitos em postgresql.conf ou pg_hba.conf)
# Aqui, por simplicidade, deixamos o default

# Voltar para o usuário root para iniciar serviços a partir do ENTRYPOINT
USER root

# Expõe a porta em que a aplicação Go vai rodar, por exemplo a 8080
EXPOSE 8080

# ENTRYPOINT que inicia o PostgreSQL e, em seguida, a aplicação
# Primeiro, o PostgreSQL é iniciado em segundo plano
# Depois a aplicação Go é executada
ENTRYPOINT \
  bash -c "su-exec postgres pg_ctl -D $PGDATA -o '-c listen_addresses=*' -w start && \
           myapp"
```

**O que este Dockerfile faz:**

- **Fase de Build (builder):**
    
    - Usa uma imagem `golang:1.20-alpine` para compilar o código Go.
    - Copia o código-fonte, executa `go mod download` para baixar as dependências e compila a aplicação em um binário estático.
- **Fase Final:**
    
    - Usa uma imagem `alpine` mais leve como base.
    - Instala o [[PostgreSQL]] e configura o diretório de dados.
    - Copia o binário compilado da primeira fase para a imagem final.
    - Inicializa o cluster do PostgreSQL.
    - Ao iniciar o container (`docker run`), o ENTRYPOINT primeiro inicia o PostgreSQL em segundo plano e depois executa a aplicação Go.

**Importante:**

- Este é um exemplo didático. Em ambiente real, normalmente a aplicação ficaria em um container separado do banco de dados, gerenciado pelo Docker [[Compose]] ou [[Kubernetes]], por exemplo.
- Ajustes de segurança, configurações de banco e aplicação, e estratégias de dados persistentes via volumes podem ser necessários conforme o caso.


### Executando o Dockerfile para criar a imagem

Para criar a imagem a partir do Dockerfile, você primeiro precisa navegar até o diretório onde o Dockerfile está localizado e então executar o comando:

```bash
docker build -t nome_da_imagem:tag .
```

- `nome_da_imagem` é um nome arbitrário que você escolhe para a sua imagem.
- `tag` é uma etiqueta que ajuda a versionar a imagem (por exemplo, `v1.0`).
- O `.` no final indica ao Docker para usar o Dockerfile localizado no diretório atual.

Por exemplo:

```bash
docker build -t minhaaplicacao:latest .
```

Esse comando irá ler o Dockerfile, construir a imagem conforme as instruções e criar uma nova imagem chamada `minhaaplicacao` com a tag `latest`.

Depois que a imagem estiver pronta, você pode executar um container baseado nela com:

```bash
docker run -p 8080:8080 minhaaplicacao:latest
```

Aqui, o `-p 8080:8080` é um exemplo de mapeamento de porta, garantindo que a aplicação que roda dentro do container na porta 8080 fique acessível na porta 8080 da sua máquina local.