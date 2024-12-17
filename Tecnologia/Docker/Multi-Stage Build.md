---
tags:
  - Tecnologia
  - Docker
---
Vamos otimizar o Dockerfile usado na explicação do que é um [[Dockerfile]] para qual finalidade ele foi criado, utilizando **Multi-Stage Build** para que a imagem final seja menor e mais eficiente.

---

### **Dockerfile Original**

O Dockerfile que você forneceu faz o seguinte:

1. Usa a imagem base `golang` (pesada).
2. Baixa as dependências.
3. Compila a aplicação.
4. A imagem final contém o Go SDK e todo o código-fonte, mesmo que não sejam necessários em runtime.

---

### **Dockerfile Otimizado com Multi-Stage Build**

```dockerfile
# Etapa 1: Construção do binário da aplicação Go
FROM golang:1.21 AS builder

# Definir diretório de trabalho
WORKDIR /usr/src/app

# Copiar arquivos de dependências do Go (go.mod e go.sum, se existirem)
COPY go.mod go.sum ./
RUN go mod download

# Copiar o restante do código da aplicação
COPY . ./

# Compilar a aplicação Go
RUN go build -o myapp .

# Etapa 2: Imagem final otimizada
FROM alpine:latest

# Copiar o binário compilado da etapa de construção
COPY --from=builder /usr/src/app/myapp /usr/bin/myapp

# Definir o comando padrão para execução
CMD ["myapp"]
```

---

### **Diferenças e Melhorias**

1. **Uso do Multi-Stage Build**:
    
    - Dividimos em duas etapas usando dois comandos `FROM`:
        - **Primeira etapa** (`golang:1.21 AS builder`): constrói o binário.
        - **Segunda etapa** (`alpine:latest`): prepara a imagem final.
2. **Imagem Final Leve**:
    
    - Usamos `alpine:latest` como imagem final. É uma imagem Linux **extremamente leve** (~5MB).
    - Copiamos apenas o **binário compilado** da primeira etapa com:
        
        ```dockerfile
        COPY --from=builder /usr/src/app/myapp /usr/bin/myapp
        ```
        
3. **Sem Ferramentas Desnecessárias**:
    
    - A imagem final **não inclui o Go SDK**, o código-fonte ou outras dependências desnecessárias.
    - Contém apenas o **executável** `myapp`.
4. **Maior Segurança**:
    
    - Imagens menores têm **menor superfície de ataque**.

---

### **Comparação de Tamanho**

Se compararmos os tamanhos das imagens:

- **Original**: ~900MB (contém o Go SDK e todo o código-fonte).
- **Multi-Stage Build**: ~10MB (usa apenas o binário compilado e a base Alpine).

---

### **Como Funciona na Prática**

1. Durante a **primeira etapa**:
    
    - O código Go é compilado em um binário (`myapp`).
2. Durante a **segunda etapa**:
    
    - O binário compilado é copiado para uma imagem leve baseada no Alpine Linux.
    - Somente o **executável** será usado.
3. Quando o container for iniciado:
    
    - O comando **`myapp`** será executado, conforme definido no `CMD`.

---

### **Resultado Final**

Com o **Multi-Stage Build**, sua aplicação Go:

- **Reduz o tamanho da imagem final** drasticamente.
- **Melhora a performance** no build, deploy e execução.
- Fica **mais segura e otimizada** para produção.

---

Se quiser, posso te mostrar como **construir** e **rodar** essa imagem! 🚀