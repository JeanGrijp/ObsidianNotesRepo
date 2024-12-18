---
tags:
  - Tecnologia
  - Docker
---
A instrução `ADD` no [[Dockerfile]] é usada para **copiar arquivos ou diretórios** do host para o [[container]], assim como o `COPY`, mas oferece funcionalidades adicionais. Embora seja poderosa, é menos recomendada para uso geral devido ao risco de comportamento inesperado.
### Como funciona:

1. **Copia arquivos ou diretórios**:
    - Como o `COPY`, transfere arquivos do sistema host para o sistema de arquivos do container.
2. **Comportamento adicional**:
    - **Descompactação automática**:
        - Se a origem for um arquivo `.tar`, ele será descompactado automaticamente no container.
    - **Download de arquivos remotos**:
        - Permite baixar arquivos diretamente de uma URL para dentro do container.
3. **Formato básico**:
    
    ```Dockerfile
ADD [origem] [destino]
```


### Exemplos:

#### Copiar arquivos como `COPY`:

```Dockerfile
ADD app.js /usr/src/app/
```

- Copia `app.js` do host para o diretório `/usr/src/app/` no container.

#### Descompactar arquivos `.tar` automaticamente:

```Dockerfile
ADD app.tar.gz /usr/src/app/
```

- Copia e descompacta `app.tar.gz` no diretório `/usr/src/app/`.

#### Baixar arquivos de uma URL:

```Dockerfile
ADD https://example.com/file.tar.gz /usr/src/app/
```

- Baixa o arquivo de uma URL e o coloca no container. (⚠️ Não recomendado por razões de segurança).

### Diferença entre `ADD` e `COPY`:

|**Aspecto**|**ADD**|**COPY**|
|---|---|---|
|Copiar arquivos|Sim|Sim|
|Descompactar `.tar`|Sim|Não|
|Baixar de URLs|Sim|Não|
|Comportamento simples|Não (tem lógica adicional)|Sim (apenas copia)|

### Quando usar `ADD`?

1. **Necessidade de descompactar arquivos `.tar` automaticamente**:
    - Exemplo: Adicionar arquivos de uma aplicação pré-empacotada.
2. **Necessidade de baixar de uma URL** (não recomendado, mas possível):
    - Apenas em cenários muito específicos e controlados.
3. **Alternativa ao `COPY` quando precisa de funcionalidade adicional**:
    - Se você não precisa das funcionalidades extras, **use `COPY`**.


### Cuidados ao usar `ADD`:

1. **Segurança**:
    - O comportamento de baixar arquivos de URLs pode ser inseguro e imprevisível.
    - Baixar arquivos externamente pode introduzir vulnerabilidades.
2. **Previsibilidade**:
    - A descompactação automática de `.tar` pode não ser desejada em alguns casos e pode confundir.
3. **Tamanho da imagem**:
    - Arquivos não utilizados, especialmente ao baixar ou descompactar, podem aumentar desnecessariamente o tamanho da imagem.