---
tags:
  - Tecnologia
  - Docker
---
A instrução `COPY` no [[Dockerfile]] é usada para **copiar arquivos ou diretórios do sistema host para o sistema de arquivos do container**. Ela é muito útil para incluir o código-fonte, dependências ou outros arquivos necessários para a aplicação funcionar dentro do [[container]].

### Como funciona:

1. **Origem**:
    - Pode ser um arquivo ou diretório no sistema host (a máquina onde você está rodando o [[Docker]]).
    - O caminho da origem é relativo ao contexto de construção (geralmente o diretório onde o Dockerfile está localizado).
2. **Destino**:
    - É o caminho dentro do container onde os arquivos ou diretórios serão copiados.
3. **Formato básico**:
    
    ```Dockerfile
COPY [origem] [destino]
```

### Exemplos:

#### Copiando um único arquivo:

```Dockerfile
COPY index.html /usr/share/nginx/html/
```

- Copia `index.html` do sistema host para o diretório `/usr/share/nginx/html/` no container.

#### Copiando múltiplos arquivos:

```Dockerfile
COPY package.json package-lock.json /app/
```

- Copia `package.json` e `package-lock.json` para o diretório `/app` no container.

#### Copiando um diretório:

```Dockerfile
COPY ./src /app/src
```

- Copia todo o conteúdo da pasta `src` no host para `/app/src` no container.

### Características do `COPY`:

1. **Direto e Simples**:
    - Apenas copia arquivos. Não permite funcionalidades extras, como baixar arquivos de URLs ou descompactar arquivos.
2. **Respeita Permissões**:
    - Mantém as permissões dos arquivos copiados do host para o container.
3. **Relativo ao Contexto de Construção**:
    - A origem deve estar dentro do diretório de contexto configurado ao rodar `docker build`. Arquivos fora desse contexto não podem ser copiados diretamente.

### Diferença entre `COPY` e `ADD`:

- `COPY`:
    - Apenas copia arquivos/diretórios do host para o container.
    - É mais previsível e recomendado na maioria dos casos.
- `ADD`:
    - Além de copiar arquivos/diretórios, pode:
        - **Descompactar arquivos tar** automaticamente.
        - **Baixar arquivos de URLs** (não recomendado por razões de segurança).

### Benefícios do `COPY`:

1. **Simplicidade**:
    - Focado apenas em copiar, sem surpresas.
2. **Segurança**:
    - Não tem comportamento adicional que possa introduzir vulnerabilidades (como downloads de URLs).