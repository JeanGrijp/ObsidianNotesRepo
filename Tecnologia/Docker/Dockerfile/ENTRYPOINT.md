---
tags:
  - Tecnologia
  - Docker
---
O **`ENTRYPOINT`** é como um comando **automático** que o container vai rodar **sempre que for iniciado**. Ele define **o que o [[container]] faz** por padrão.

Pense assim:

- Você está criando um container que sempre roda um script ou programa específico.
- O `ENTRYPOINT` garante que, quando você rodar o container, **esse comando será executado** automaticamente.

### Exemplo simples:

Imagine que você quer que seu container rode o comando `python app.py` sempre que ele for iniciado. No [[Dockerfile]], você escreve:

```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

Agora, toda vez que rodar o container com:

```bash
docker run meu-container
```

O container vai automaticamente executar o comando:

```bash
python app.py
```

### E se você quiser passar **argumentos extras**?

O comando `ENTRYPOINT` é fixo, mas você pode adicionar **argumentos** quando rodar o container:

```bash
docker run meu-container --debug
```

Isso faz com que o container execute:

```bash
python app.py --debug
```

### Como é diferente do `CMD`?

- **`ENTRYPOINT`**: Define **o que sempre será executado**, é como o "motor do container".
- **`CMD`**: Dá opções ou argumentos **padrão**, que você pode substituir.

Se usar os dois juntos:

```Dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--debug"]
```

Quando você rodar o container sem argumentos, ele fará:

```bash
python app.py --debug
```

Se passar algo no `docker run`:

```bash
docker run meu-container --prod
```

O comando final será:

```bash
python app.py --prod
```


# Vamos explorar mais exemplos para facilitar o entendimento do `ENTRYPOINT`!

### 1. **Criar um container que sempre imprime uma mensagem**

Dockerfile:

```Dockerfile
ENTRYPOINT ["echo", "Olá, este é o meu container!"]
```

Quando você rodar o container:

```bash
docker run meu-container
```

O container exibirá:

```
Olá, este é o meu container!
```

Se você passar algo no terminal, isso será adicionado ao comando:

```bash
docker run meu-container "e eu aceito argumentos!"
```

Saída:

```
Olá, este é o meu container! e eu aceito argumentos!
```

### 2. **Um servidor básico com Node.js**

Dockerfile:

```Dockerfile
FROM node:16
COPY app.js /app/app.js
WORKDIR /app
ENTRYPOINT ["node", "app.js"]
```

O container sempre executará o servidor com o comando:

```bash
node app.js
```

Se você rodar o container com argumentos adicionais:

```bash
docker run meu-container --port=3000
```

O comando será:

```bash
node app.js --port=3000
```

### 3. **Usar `ENTRYPOINT` para um script Bash**

Dockerfile:

```Dockerfile
FROM ubuntu:20.04
COPY script.sh /script.sh
RUN chmod +x /script.sh
ENTRYPOINT ["/script.sh"]
```

O container sempre executará o script `script.sh`. Quando você rodar:

```bash
docker run meu-container
```

O script será executado. Se você passar argumentos:

```bash
docker run meu-container argumento1 argumento2
```

O comando será:

```bash
/script.sh argumento1 argumento2
```

### 4. **Combinando `ENTRYPOINT` e `CMD`**

Dockerfile:

```Dockerfile
FROM python:3.10
COPY script.py /app/script.py
WORKDIR /app
ENTRYPOINT ["python", "script.py"]
CMD ["--verbose"]
```

Se rodar o container sem argumentos:

```bash
docker run meu-container
```

O comando será:

```bash
python script.py --verbose
```

Se passar algo no `docker run`:

```bash
docker run meu-container --quiet
```

O comando será:

```bash
python script.py --quiet
```

### 5. **Criar um container que funciona como executável**

Dockerfile:

```Dockerfile
FROM alpine:3.18
ENTRYPOINT ["ls"]
```

Quando você rodar:

```bash
docker run meu-container /usr
```

O comando será:

```bash
ls /usr
```

### 6. **Baixar conteúdo automaticamente**

Dockerfile:

```Dockerfile
FROM curlimages/curl:latest
ENTRYPOINT ["curl"]
```

Rodando o container:

```bash
docker run meu-container https://example.com
```

O comando será:

```bash
curl https://example.com
```

Esses exemplos mostram como o `ENTRYPOINT` ajuda a criar containers que executam comandos ou scripts automaticamente, mas permitem flexibilidade para adicionar argumentos na execução. Precisa de mais exemplos ou ajustes? 😊
### Resumo:

- O `ENTRYPOINT` **fixa** o comando principal do container.
- Você pode adicionar argumentos quando rodar o container.
- É útil para criar containers que sempre executam uma tarefa específica, como um script ou servidor.
