---
tags:
  - Tecnologia
  - Docker
---
Containers são ambientes isolados no [[docker]], eles guardam toda a configuração que a aplicação precisa para ser executada. De forma técnica, um **container** em Docker é uma **instância leve e isolada** que roda uma aplicação ou serviço em um ambiente padronizado, utilizando tecnologias do kernel do Linux, como **[[namespaces]]** e **[[cgroups]]**, para fornecer isolamento de processos, recursos e segurança.

### **Funcionamento Técnico de um Container Docker**

1. **Imagens como Base**
    - Um container é baseado em uma **[Imagem](Imagens.md) Docker**, que é um arquivo imutável contendo:
        - O sistema de arquivos do container (root filesystem).
        - Bibliotecas, dependências e a aplicação a ser executada.
    - As imagens são **layered** (em camadas), permitindo reutilização e economia de armazenamento.
2. **Isolamento com Namespaces**
    - O Docker usa **namespaces** do Linux para isolar containers:
        - **PID namespace**: Cada container tem sua própria visão de processos.
        - **Net namespace**: Containers têm interfaces de rede separadas.
        - **Mount namespace**: Cada container tem seu próprio sistema de arquivos.
        - **User namespace**: Mapas de usuários garantem segurança no host.
    
    Isso faz com que o container "enxergue" apenas os recursos que pertencem a ele, como se fosse um sistema independente.
    
3. **Controle de Recursos com cgroups**
    - O Docker usa **cgroups** (Control Groups) para gerenciar o uso de recursos, como:
        - Limites de CPU.
        - Memória alocada.
        - Rede e I/O de disco.
    - Isso evita que um container consuma todos os recursos do host, protegendo o sistema e outros containers.
4. **Camadas de Sistema de Arquivos (UnionFS)**
    - Containers utilizam sistemas de arquivos empilháveis, como o **OverlayFS**, que permitem:
        - Reutilização de camadas de imagens.
        - Escrita no sistema de arquivos em uma camada superior, sem alterar as camadas de base.
    - Isso torna os containers leves e eficientes, pois compartilham partes comuns entre si.
5. **Processos do Container**
    - Cada container executa como um **processo isolado** no host.
    - O Docker não cria um kernel separado para cada container, o que os torna mais leves do que máquinas virtuais.
6. **Networking**
    - O Docker configura automaticamente redes para containers, como:
        - Ponte padrão (_bridge network_), que conecta containers ao host.
        - Redes definidas pelo usuário para comunicação direta entre containers.

### **Resumo Técnico**

- Um container Docker é uma **instância de uma imagem**, que roda como um **processo isolado** no host.
- Ele utiliza:
    - **Namespaces** para isolamento de processos, rede, e sistema de arquivos.
    - **Cgroups** para limitar o uso de recursos.
    - **OverlayFS** para gerenciar o sistema de arquivos leve e compartilhado.
- Compartilha o kernel do host, diferentemente de máquinas virtuais, mas ainda mantém um ambiente isolado para executar aplicações.

Essa abordagem técnica garante que os containers sejam:

- **Leves**: Sem overhead de kernel adicional.
- **Eficientes**: Compartilhando recursos sem interferência.
- **Seguros**: Com isolamento garantido pelo kernel.

# Subindo um container a partir de um Dockerfile


Se você já tem um **[[Dockerfile]]**, os comandos para criar a [Imagem](Imagens.md) e subir um container com ela são:

### 1. **Criar a Imagem**

Use o comando abaixo para criar a imagem a partir do **Dockerfile**:

```bash
docker build -t nome-da-imagem .
```

- **`-t nome-da-imagem`**: Dá um nome à imagem (substitua `nome-da-imagem` pelo nome desejado).
- **`.`**: Especifica o contexto de construção (o diretório onde está o Dockerfile).

### 2. **Subir um Container com a Imagem**

Após criar a imagem, execute o seguinte comando para rodar o container:

```bash
docker run -d --name nome-do-container -p 8080:80 nome-da-imagem
```

Explicação dos parâmetros:

- **`-d`**: Executa o container em segundo plano.
- **`--name nome-do-container`**: Dá um nome ao container.
- **`-p 8080:80`**: Mapeia a porta **80** do container para a porta **8080** no host. Altere conforme necessário.
- **`nome-da-imagem`**: Substitua pelo nome da imagem que você criou.

### Exemplo Prático

Se o seu Dockerfile cria uma aplicação que roda na porta **3000**, faça assim:

1. **Crie a imagem**:

    ```bash
docker build -t minha-aplicacao .
```

2. **Suba o container**:

    ```bash
docker run -d --name app-container -p 3000:3000 minha-aplicacao
```