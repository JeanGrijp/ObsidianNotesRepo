---
tags:
  - Tecnologia
  - Docker
---
A instrução `ENTRYPOINT` no [[Dockerfile]] é usada para definir o **comando principal** que será executado quando o [[container]] iniciar. Diferente de `CMD`, o `ENTRYPOINT` é mais rígido, pois permite configurar o container para que ele sempre execute o comando especificado, mesmo se argumentos forem passados durante a execução.

### Como funciona:

- O `ENTRYPOINT` define o comando base que o container sempre executará.
- Você pode passar argumentos adicionais no momento de executar o container usando `docker run`.

### Formatos do `ENTRYPOINT`:

1. **Execução**:
    - Define o comando como uma lista de strings.

    ```Dockerfile
    ENTRYPOINT ["comando", "arg1", "arg2"]
    ```

2. **Shell**:
    - Executa o comando em um shell.

    ```Dockerfile
    ENTRYPOINT comando arg1 arg2
    ```

### Diferença entre `ENTRYPOINT` e `CMD`:

- **`ENTRYPOINT`**:
    - Focado em definir o comando principal que será executado no container.
    - É imutável, ou seja, argumentos fornecidos via `docker run` são adicionados ao comando definido no `ENTRYPOINT`.
- **`CMD`**:
    - Define o comando padrão que pode ser substituído ao rodar o container.

### Exemplos:

#### Configurando `ENTRYPOINT`:

```Dockerfile
ENTRYPOINT ["python", "app.py"]
```

- Aqui, o container sempre executará o comando `python app.py` ao ser iniciado.

#### Adicionando argumentos na execução:

```bash
docker run meu-container --arg1 valor1
```

- O comando final será: `python app.py --arg1 valor1`.

#### Combinando `ENTRYPOINT` e `CMD`:

Você pode usar `ENTRYPOINT` para o comando principal e `CMD` para fornecer valores padrão para argumentos.

```Dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--arg1", "valor1"]
```

- O comando final será: `python app.py --arg1 valor1`.
- Se argumentos forem fornecidos via `docker run`, eles substituirão o `CMD`.

### Quando usar `ENTRYPOINT`:

- Quando você quer que o container sempre execute um comando específico.
- Para criar containers que se comportam como executáveis, aceitando argumentos personalizados.


### Cuidados ao usar:

1. **Flexibilidade**:
    - Se o comando precisa ser facilmente substituível, prefira `CMD`.
2. **Modo Shell**:
    - Usar o formato shell pode causar problemas com sinais e comportamentos inesperados. Prefira o formato exec (`[]`).