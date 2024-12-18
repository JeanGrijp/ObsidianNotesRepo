---
tags:
  - Tecnologia
  - Docker
---
A tag `alpine` em [[imagens]] [[Docker]] refere-se a uma versão da imagem baseada no **Alpine Linux**, que é uma distribuição Linux **extremamente leve e minimalista**. É amplamente usada para criar imagens Docker menores e mais rápidas.

### Principais Características da Tag `alpine`:

1. **Imagem Minimalista**:
    
    - Alpine é projetado para ser pequeno e eficiente.
    - A imagem base ocupa apenas cerca de **5 MB**, enquanto distribuições como Ubuntu podem ter mais de 20 MB.
2. **Performance**:
    
    - Ideal para cenários onde o tamanho da imagem e o tempo de inicialização são importantes.
3. **Segurança**:
    
    - Menos pacotes e serviços reduzem a superfície de ataque, tornando-a mais segura.
4. **Gerenciador de Pacotes Leve (`apk`)**:
    
    - Usa o `apk` (Alpine Package Keeper), que é simples e eficiente.

### Quando usar `alpine`:

- **Ambientes de Produção**:
    - Para criar imagens finais leves.
- **Aplicações Simples**:
    - Ideal para microsserviços ou aplicações com dependências mínimas.
- **Redução de Custos**:
    - Em serviços baseados em nuvem, imagens menores economizam largura de banda e espaço de armazenamento.

### Exemplo de Uso:

#### Dockerfile com `node:alpine`

```Dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]
```

- Aqui, o `node:16-alpine` reduz o tamanho da imagem base do Node.js.

### Cuidados ao usar `alpine`:

1. **Compatibilidade**:
    - Nem todas as bibliotecas funcionam perfeitamente no Alpine devido ao uso do **musl libc** (em vez de glibc).
2. **Mais Configuração**:
    - Por ser minimalista, pode exigir instalações adicionais para ferramentas ou dependências comuns, como `bash`, `glibc`, ou compiladores.