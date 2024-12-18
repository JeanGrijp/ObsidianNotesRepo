---
tags:
  - Tecnologia
  - Docker
---
A instrução **`ENV`** no [[Dockerfile]] é usada para **definir variáveis de ambiente** no ambiente do container. Essas variáveis podem ser utilizadas durante a construção ou execução do container, facilitando a configuração de valores dinâmicos, como senhas, configurações ou caminhos.

### Como funciona:

1. **Define uma variável de ambiente** no container.
2. Essa variável estará disponível para **todos os comandos subsequentes** no Dockerfile e também dentro do container em execução.
3. Sintaxe básica:    
    ```Dockerfile
ENV NOME_VARIAVEL valor
```

### Exemplos:

#### 1. Definir uma variável simples

```Dockerfile
ENV APP_ENV=production
```

Agora, a variável `APP_ENV` estará disponível no container. Por exemplo, em um script ou aplicação, você pode acessá-la:

```bash
echo $APP_ENV
```

Saída:

```
production
```

#### 2. Usar a variável no Dockerfile

Você pode usar as variáveis definidas com `ENV` em comandos subsequentes no Dockerfile:

```Dockerfile
ENV APP_ENV=production
ENV APP_HOME=/usr/src/app
WORKDIR $APP_HOME
```

Aqui, o diretório de trabalho será configurado para `/usr/src/app` usando a variável `APP_HOME`.

#### 3. Variáveis para comandos de instalação

```Dockerfile
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y tzdata
```

A variável `DEBIAN_FRONTEND=noninteractive` é usada para evitar prompts interativos durante a instalação de pacotes no Ubuntu/Debian.

### Como usar no momento de rodar o container:

Você pode sobrescrever variáveis definidas no Dockerfile usando a flag `-e` ou `--env` no comando `docker run`.

#### Exemplo:

Dockerfile:

```Dockerfile
ENV PORT=3000
```

Rodando o container:

```bash
docker run -e PORT=4000 meu-container
```

Aqui, o valor da variável `PORT` será sobrescrito para `4000`.

### Boa prática: múltiplas variáveis em uma linha

Para melhorar a legibilidade, você pode definir várias variáveis em uma única instrução:

```Dockerfile
ENV APP_ENV=production \
   APP_PORT=3000 \
   APP_DEBUG=false
```

### Quando usar o `ENV`:

1. **Configurações padrão**:
    - Definir valores padrão que a aplicação pode usar (ex.: `APP_ENV=production`).
2. **Padrões que podem ser sobrescritos**:
    - Permite passar valores diferentes no momento da execução com `docker run -e`.
3. **Reduzir repetição**:
    - Usar variáveis no Dockerfile evita repetir valores em comandos subsequentes.

### Cuidados:

1. **Informações sensíveis**:
    - Não use `ENV` para armazenar senhas ou chaves secretas diretamente no Dockerfile, pois elas ficam visíveis na imagem. Use arquivos externos ou ferramentas como Docker Secrets.
2. **Ordem de execução**:
    - A variável definida só estará disponível **a partir do momento em que for declarada**.

### Resumo:

- O `ENV` define variáveis de ambiente para o container.
- Ele é útil para configurar valores padrão e dinâmicos.
- Pode ser sobrescrito no momento da execução com `docker run -e`.
- Evite usar `ENV` para dados sensíveis; prefira métodos mais seguros.