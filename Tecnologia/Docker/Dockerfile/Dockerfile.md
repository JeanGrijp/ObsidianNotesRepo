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

Um exemplo de um Dockerfile que constrói uma aplicação [[Go]]. É importante ressaltar que, na prática, é comum separar a aplicação do banco de dados em dois [Container](Container.md) distintos (um container para a aplicação e outro para o banco), seguindo o padrão de [[microsserviços]] e aproveitando melhor a infraestrutura do [[Docker]]. 

```dockerfile
# Etapa 1: Construção do binário da aplicação Go

FROM golang

# Definir diretório de trabalho

WORKDIR /usr/cmd/server

# Copiar arquivos de dependências do Go (go.mod e go.sum, se existirem)

COPY go.mod go.sum /usr/

WORKDIR /usr

RUN go mod download

# Copiar o restante do código da aplicação

COPY . /usr

WORKDIR /usr/cmd/server

# Compilar a aplicação Go

RUN go build -o myapp .

CMD ["./myapp"]
```

# **O que este Dockerfile faz:**

### **1. Etapa 1: Construção do binário da aplicação Go**

O objetivo dessa etapa é **compilar** a aplicação Go e preparar o ambiente de execução.

#### **1.1 FROM golang**

```dockerfile
FROM golang
```

- O **`FROM`** especifica a imagem base.
- Aqui é usada a imagem oficial do Go, disponível no Docker Hub.
- Essa imagem já possui o **Go SDK** e ferramentas necessárias para compilar e rodar aplicações Go.

---

#### **1.2 WORKDIR /usr/cmd/server**

```dockerfile
WORKDIR /usr/cmd/server
```

- Define o **diretório de trabalho** onde os comandos seguintes serão executados.
- Evita a necessidade de especificar o caminho em comandos posteriores.
- Diretório inicial: `/usr/cmd/server`.

---

#### **1.3 COPY go.mod go.sum /usr/**

```dockerfile
COPY go.mod go.sum /usr/
```

- Copia os arquivos **`go.mod`** e **`go.sum`** (se existirem) do diretório local para o diretório `/usr/` no container.
- **`go.mod`** e **`go.sum`** contêm informações sobre as dependências do projeto Go.

---

#### **1.4 WORKDIR /usr**

```dockerfile
WORKDIR /usr
```

- Muda o diretório de trabalho para `/usr`, onde estão os arquivos `go.mod` e `go.sum`.

---

#### **1.5 RUN go mod download**

```dockerfile
RUN go mod download
```

- **Baixa as dependências** listadas no `go.mod` para o cache do container.
- Isso garante que as dependências sejam resolvidas antes de copiar o código da aplicação, otimizando o build.

---

#### **1.6 COPY . /usr**

```dockerfile
COPY . /usr
```

- Copia **todo o código-fonte da aplicação** (diretório atual) para `/usr` no container.
- Isso inclui todos os arquivos Go e diretórios necessários.

---

#### **1.7 WORKDIR /usr/cmd/server**

```dockerfile
WORKDIR /usr/cmd/server
```

- Volta para o diretório `/usr/cmd/server`, onde está o código principal da aplicação.
- Aqui o **build** será executado.

---

#### **1.8 RUN go build -o myapp .**

```dockerfile
RUN go build -o myapp .
```

- Compila o projeto Go.
- O **`go build`** transforma o código-fonte em um **executável**.
- O flag **`-o myapp`** define o nome do binário como `myapp`.
- O ponto (`.`) indica que o código a ser compilado está no diretório atual.

---

#### **1.9 CMD ["./myapp"]**

```dockerfile
CMD ["./myapp"]
```

- Define o **comando padrão** que será executado quando o container for iniciado.
    
- Neste caso, executa o binário `myapp` gerado na etapa anterior.
    
- Diferente de `RUN`, que é usado durante a **construção da imagem**, o `CMD` define o comando para **executar no runtime** (quando o container é iniciado).
    

---

## Resumo das principais etapas:

1. **Base image**: Utiliza `golang` como imagem base.
2. **Dependências**:
    - Copia `go.mod` e `go.sum`.
    - Executa `go mod download` para baixar dependências.
3. **Código-fonte**: Copia todo o código-fonte da aplicação.
4. **Compilação**: Compila a aplicação Go com `go build`.
5. **Execução**: Define `./myapp` como o comando padrão ao iniciar o container.

---

Se precisar adicionar algo mais, como **multi-stage builds** para otimizar o tamanho da imagem, me avise! 😊

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