---
tags:
  - Tecnologia
  - Docker
---
O **`CMD`** no [[Dockerfile]] é usado para **definir o comando padrão** que o container executará **quando for iniciado**. Porém, diferentemente do `ENTRYPOINT`, o **CMD pode ser substituído** se você passar argumentos ao rodar o container com `docker run`.
### Como o `CMD` funciona:

1. O comando especificado em `CMD` **só será executado se você não passar outro comando ao rodar o container**.
2. Se você passar um comando ao rodar o container, o `CMD` será **ignorado**.
3. Ele geralmente é usado para fornecer **argumentos padrão** ou para executar scripts simples.

### Formatos do `CMD`:

1. Execução:
    - Define o comando como uma lista de strings.
    ```Dockerfile
CMD ["comando", "arg1", "arg2"]
```
1. **Shell**:
    - Executa o comando dentro de um shell.
    ```Dockerfile
CMD comando arg1 arg2
```

### Exemplos práticos:

#### 1. Executar um script com argumento padrão

Dockerfile:

```Dockerfile
FROM node:16
COPY app.js /app/
WORKDIR /app
CMD ["node", "app.js"]
```

Quando você rodar o container:

```bash
docker run meu-container
```

O comando executado será:

```bash
node app.js
```

Se você passar outro comando:

```bash
docker run meu-container node -v
```

O comando será:

```bash
node -v
```

#### 2. Usando argumentos padrão

Dockerfile:

```Dockerfile
FROM ubuntu:20.04
CMD ["echo", "Olá, mundo!"]
```

Rodando o container sem argumentos:

```bash
docker run meu-container
```

Saída:

```
Olá, mundo!
```

Se você passar algo diferente:

```bash
docker run meu-container echo "Substituí o CMD!"
```

Saída:

```
Substituí o CMD!
```

#### 3. Configurar argumentos padrão para um script

Dockerfile:

```Dockerfile
FROM python:3.10
COPY script.py /app/
WORKDIR /app
CMD ["python", "script.py", "--verbose"]
```

Se rodar o container sem argumentos:

```bash
docker run meu-container
```

O comando será:

```bash
python script.py --verbose
```

Se passar novos argumentos:

```bash
docker run meu-container python script.py --quiet
```

O comando será:

```bash
python script.py --quiet
```

### Diferença entre `CMD` e `ENTRYPOINT`:

|**Aspecto**|**CMD**|**ENTRYPOINT**|
|---|---|---|
|Substituível no `run`|Sim|Não (argumentos são adicionados)|
|Propósito|Define comando padrão|Define o comando principal|
|Flexibilidade|Mais flexível|Mais rígido e específico|

### Combinar `CMD` com `ENTRYPOINT`:

Você pode usar `ENTRYPOINT` para definir o comando principal e `CMD` para definir os argumentos padrão:

Dockerfile:

```Dockerfile
FROM ubuntu:20.04
ENTRYPOINT ["echo"]
CMD ["Olá, mundo!"]
```

Sem argumentos:

```bash
docker run meu-container
```

Saída:

```
Olá, mundo!
```

Com novos argumentos:

```bash
docker run meu-container "Substituí o CMD!"
```

Saída:

```
Substituí o CMD!
```