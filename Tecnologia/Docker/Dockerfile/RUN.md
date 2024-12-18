---
tags:
  - Tecnologia
  - Docker
---
A instrução `RUN` no [[Dockerfile]] é usada para **executar comandos no ambiente do [[container]] durante a construção da [Imagens](Imagens.md)**. Isso é útil para instalar dependências, configurar o ambiente ou realizar outras tarefas necessárias antes do container estar pronto para execução.

### Como funciona:

1. **Executa comandos**:
    - Qualquer comando que você poderia executar em um terminal no sistema base da imagem pode ser usado com `RUN`.
2. **Impacto na imagem**:
    - O resultado de cada `RUN` é salvo como uma **nova camada** na imagem final.
3. **Formato básico**:
    - **Shell** (mais comum):
        
        ```Dockerfile
RUN comando
```

```Dockerfile
RUN ["comando", "argumento1", "argumento2"]
```

### Exemplos:

#### Instalando pacotes:

```Dockerfile
RUN apt-get update && apt-get install -y curl
```

- Atualiza a lista de pacotes e instala o `curl` no container.

#### Configurando permissões:

```Dockerfile
RUN adduser --disabled-password meuusuario
```

- Cria um novo usuário no container.

#### Limpando após instalação:

```Dockerfile
RUN apt-get update && apt-get install -y curl && apt-get clean
```

- Evita que arquivos desnecessários aumentem o tamanho da imagem.

### Boa Prática: Unir Comandos

- Cada `RUN` cria uma camada na imagem, então **combine comandos relacionados** para reduzir o número de camadas e o tamanho da imagem.

#### Antes:

```Dockerfile
RUN apt-get update
RUN apt-get install -y curl
```

- Cria duas camadas.

#### Depois:

```Dockerfile
RUN apt-get update && apt-get install -y curl
```

- Cria uma única camada.

### Diferença entre `RUN`, `CMD` e `ENTRYPOINT`:

| **Instrução**      | **Propósito**                                                                 |
| ------------------ | ----------------------------------------------------------------------------- |
| **RUN**            | Executa comandos **durante a construção** da imagem.                          |
| **[[CMD]]**        | Define o comando padrão para ser executado **quando o container é iniciado**. |
| **[[ENTRYPOINT]]** | Define o comando principal e imutável do container.                           |

### Cuidados ao usar `RUN`:

1. **Tamanho da Imagem**:
    - Use `&&` para combinar comandos e reduza o número de camadas.
2. **Cache**:
    - O Docker usa cache para comandos `RUN`, então mudanças em comandos anteriores podem invalidar o cache.
3. **Limpeza**:
    - Remova arquivos temporários ou pacotes desnecessários após instalá-los para reduzir o tamanho da imagem.