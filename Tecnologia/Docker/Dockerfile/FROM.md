---
tags:
  - Tecnologia
  - Docker
---
A instrução `FROM` no [[Dockerfile]] é essencial e é sempre a primeira linha de um Dockerfile válido (com raras exceções, como quando se usa um `ARG` antes dela). Ela serve para especificar a **[Imagem](Imagens.md) base** a partir da qual o container será construído.

### Como o `FROM` funciona:

1. **Especificação da imagem base**:
    
    - A instrução `FROM` indica a imagem que será usada como ponto de partida para a construção do seu [[container]].
    - Essa imagem pode ser qualquer uma disponível no **[[Docker Hub]]** ou em outros repositórios de imagens, públicos ou privados.
    - Exemplo:
        
		```Dockerfile
FROM node:16
```
        
        Nesse caso, o Docker usará a imagem do Node.js na versão 16.
2. **Uso de tags**:
    
    - A imagem base pode ter diferentes versões, identificadas por **tags**.
    - Exemplo: `node:16`, `ubuntu:20.04`, `alpine:3.18`.
    - Você também pode usar `latest` para pegar a versão mais recente, mas isso é desencorajado em produção porque a imagem pode mudar inesperadamente.
3. **[Multi-Stage Builds](Multi-Stage%20Build.md)**:
    
    - O `FROM` pode ser usado várias vezes em um mesmo Dockerfile para criar **multi-stage builds**.
    - Cada instrução `FROM` inicia um novo estágio de construção.
    - Exemplo:

        ```Dockerfile
# Primeiro estágio: Build
FROM node:16 AS build
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Segundo estágio: Produção
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```
        
        Nesse caso, o primeiro estágio constrói o projeto e o segundo estágio prepara a imagem para produção.
4. **Imagens customizadas**:
    - Você pode criar suas próprias imagens base e utilizá-las em projetos específicos:
        
        ```Dockerfile
FROM minha-imagem-personalizada:1.0
```
        

### Utilidade do `FROM`:

1. **Facilidade de reutilização**:
    
    - Reaproveita imagens já otimizadas para casos de uso específicos, como `python`, `node`, ou `nginx`.
2. **Flexibilidade**:
    
    - Permite escolher uma base leve, como [[alpine]], ou uma mais completa, como `ubuntu`, dependendo das necessidades do projeto.
3. **Multi-stage builds**:
    
    - Reduz o tamanho final da imagem ao usar diferentes estágios para construir e otimizar o container.
4. **Padronização**:
    
    - Usando uma base bem definida, sua equipe pode garantir consistência nos ambientes de desenvolvimento, teste e produção.


## As Build

O uso de `AS build` na instrução `FROM` indica que estamos **nomeando o estágio atual da construção** em um Dockerfile com **multi-stage builds**. Esse nome (`build`, no caso) pode ser utilizado posteriormente no mesmo Dockerfile para **referenciar arquivos ou diretórios gerados nesse estágio**.

### O que o `AS build` faz?

1. **Nomeação do estágio**:
    
    - Ao adicionar `AS build`, você está dando um nome (no caso, `build`) ao estágio de construção.
    - Esse nome pode ser usado mais tarde no Dockerfile.
2. **Reutilização entre estágios**:
    
    - Outro estágio pode copiar arquivos diretamente desse estágio usando a instrução `COPY --from=build`.

### Exemplo com Explicação

```Dockerfile
# Estágio 1: Build do projeto
FROM node:16 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Estágio 2: Configuração para produção
FROM nginx:alpine
# Copiando os arquivos construídos no estágio 'build' para o Nginx
COPY --from=build /app/build /usr/share/nginx/html
```

#### Explicação do Exemplo:

1. **Primeiro estágio (`AS build`)**:
    
    - O Node.js é usado para instalar dependências e construir o projeto.
    - O nome `build` foi dado ao estágio para ser referenciado mais tarde.
2. **Segundo estágio (`nginx:alpine`)**:
    
    - O [[Nginx]] é usado como servidor web para hospedar os arquivos estáticos.
    - A instrução `COPY --from=build` copia os arquivos criados no diretório `/app/build` do estágio `build` para o diretório `/usr/share/nginx/html` da imagem final.

### Benefícios do `AS build`:

1. **Redução do tamanho da imagem final**:
    - As ferramentas de desenvolvimento (Node.js, no exemplo) não estão na imagem final.
2. **Organização do Dockerfile**:
    - Nomear estágios torna o Dockerfile mais legível e fácil de entender.
3. **Eficiência**:
    - Você reaproveita o trabalho feito em estágios anteriores sem duplicação de código ou arquivos.