---
tags:
  - Tecnologia
  - Docker
---
O **Docker Compose** é uma ferramenta que simplifica o gerenciamento de aplicativos [[Docker]] compostos por múltiplos [containers](Container.md). Ele utiliza um arquivo de configuração YAML para descrever todos os serviços, [[volumes]], redes e variáveis de ambiente necessários para a aplicação, permitindo que você gerencie tudo de forma centralizada.

Em vez de executar comandos complexos para iniciar cada container, o Docker Compose permite configurar e executar todos os serviços da aplicação com apenas um ou dois comandos.

### Principais Benefícios do Docker Compose

1. **Gerenciamento Simples**: Permite configurar e executar múltiplos containers a partir de um único arquivo.
2. **Consistência**: A mesma configuração pode ser usada em ambientes diferentes, como desenvolvimento, teste e produção.
3. **Orquestração**: Facilita a comunicação entre serviços que dependem uns dos outros, como um backend e seu banco de dados.
4. **Automação**: Com poucos comandos, é possível subir, parar e limpar os containers e redes configurados.

### Como Funciona?

O Docker Compose usa um arquivo chamado `docker-compose.yml` para definir os serviços e suas configurações. Exemplo:

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=development

  database:
    image: postgres:14
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    ports:
      - "5432:5432"
```

Nesse exemplo:

- O serviço `app` é construído a partir do Dockerfile no diretório atual.
- O serviço `database` usa a imagem oficial do [[PostgreSQL]] e expõe a porta 5432.

### Comandos Básicos do Docker Compose

1. **Subir os serviços**:

    ```bash
docker-compose up
```

	Adicione `-d` para rodar em segundo plano:

    ```bash
docker-compose up -d
```

2. **Parar e remover os serviços**:

    ```bash
docker-compose down
```

3. **Verificar o status dos serviços**:

    ```bash
docker-compose ps
```

4. **Exibir logs dos containers**:

    ```bash
docker-compose logs
```

5. **Reconstruir containers (após alterar o código ou o Dockerfile)**:

    ```bash
docker-compose up --build
```

### Aplicações Típicas do Docker Compose

- Configurar ambientes de desenvolvimento com múltiplos serviços (por exemplo, frontend, backend e banco de dados).
- Criar pipelines de integração contínua (CI/CD), orquestrando testes que dependem de diferentes containers.
- Implantar aplicações completas em servidores, mantendo a consistência entre ambientes.

O Docker Compose é uma ferramenta essencial para gerenciar projetos que dependem de vários serviços interconectados, tornando o fluxo de trabalho mais eficiente e organizado.

## Da para gerenciar pelo `docker-compose.yml` a construção de imagens e gerenciamento de containers?

Ele foi projetado exatamente para isso: automatizar e centralizar essas tarefas.

### Construção de Imagens no `docker-compose.yml`

Você pode definir como as imagens devem ser construídas a partir de um **[[Dockerfile]]** no arquivo `docker-compose.yml`. Para isso, use a diretiva `build`. Exemplo:

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: minha-aplicacao:1.0
    ports:
      - "3000:3000"
```

No exemplo acima:

- `build.context`: Define o diretório que contém o **Dockerfile** (neste caso, o diretório atual `.`).
- `build.dockerfile`: Opcional; especifica o nome do **Dockerfile** (se não for o padrão `Dockerfile`).
- `image`: Define o nome e a tag da imagem resultante.

### Gerenciamento de Containers

Você também pode gerenciar os containers, definindo as configurações de rede, volumes, variáveis de ambiente e outras opções no mesmo arquivo. Exemplo:

```yaml
version: '3.8'

services:
  web:
    build:
      context: .
    ports:
      - "8080:80"
    volumes:
      - ./src:/usr/share/nginx/html
    environment:
      - NODE_ENV=production

  database:
    image: postgres:14
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
```

No exemplo acima:

- O serviço `web` será construído a partir de um **Dockerfile**.
- O serviço `database` usa uma imagem pronta do PostgreSQL.

### Comandos para Gerenciar

1. **Construir e Subir Containers**: O comando `up` automaticamente constrói as imagens definidas e gerencia os containers:

    ```bash
docker-compose up --build
```

    Isso constrói ou reconstrói as imagens (se necessário) e inicializa os serviços.

2. **Gerenciar Containers**:

    - **Parar e remover os containers, redes e volumes criados pelo Compose**:

        ```bash
docker-compose down
```

    - **Verificar containers em execução**:

        ```bash
docker-compose ps
```

    - **Reiniciar serviços específicos**:

        ```bash
docker-compose restart web
```

### Quando Usar

- **Construção de Imagens**: Sempre que você precisar personalizar a configuração de um container a partir de um **Dockerfile**.
- **Gerenciamento de Containers**: Quando sua aplicação depende de múltiplos serviços (como frontend, backend e banco de dados) e você quer orquestrá-los com simplicidade.

O `docker-compose.yml` permite gerenciar tudo isso de forma declarativa, reduzindo a necessidade de comandos manuais para cada etapa. É uma ferramenta poderosa para criar ambientes consistentes e escaláveis.

