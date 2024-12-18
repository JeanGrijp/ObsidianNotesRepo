---
tags:
  - Tecnologia
  - Docker
---
A instrução `WORKDIR` no [[Dockerfile]] é usada para **definir o diretório de trabalho dentro do [[container]]**. É o local onde os comandos subsequentes no Dockerfile, como `RUN`, `CMD`, `COPY` e `ADD`, serão executados.

### Como funciona:

1. **Define o diretório padrão**:
    
    - Após a instrução `WORKDIR`, todos os comandos que interagem com arquivos ou diretórios assumem esse caminho como base.
2. **Cria o diretório automaticamente**:
    
    - Se o diretório especificado não existir, o Docker o cria automaticamente.
3. **Melhora a organização**:
    
    - Evita a necessidade de especificar caminhos completos em comandos subsequentes.

### Exemplo de Uso:

#### Dockerfile com `WORKDIR`

```Dockerfile
FROM node:16
# Define o diretório de trabalho no container
WORKDIR /app

# Copia os arquivos para o diretório de trabalho
COPY package*.json ./
RUN npm install

# Copia o restante dos arquivos
COPY . .

# Comando executado no diretório /app
CMD ["node", "app.js"]
```

#### Sem `WORKDIR`:

```Dockerfile
FROM node:16

# Precisa especificar o caminho completo em cada comando
COPY package*.json /app/
RUN cd /app && npm install
COPY . /app
CMD ["node", "/app/app.js"]
```

- Com `WORKDIR`, o código é mais limpo e fácil de manter.

### Benefícios do `WORKDIR`:

1. **Evita repetição**:
    - Você não precisa especificar o caminho completo em comandos repetitivos.
2. **Organização**:
    - Ajuda a manter os arquivos do container organizados em um único local.
3. **Automação**:
    - O Docker cria automaticamente o diretório, caso ele não exista, reduzindo a complexidade.