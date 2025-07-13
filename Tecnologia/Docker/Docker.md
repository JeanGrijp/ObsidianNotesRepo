---
tags:
  - Tecnologia
  - Docker
---
A história do Docker é fascinante, pois ele nasceu como uma solução para problemas comuns de desenvolvedores e acabou revolucionando o mundo da tecnologia com sua abordagem de virtualização leve baseada em [containers](Container.md).

1. **O Início: Problemas de Portabilidade**  
    Antes do Docker, os desenvolvedores enfrentavam um problema clássico: "Funciona no meu computador, mas não no servidor!". Isso acontecia porque aplicações dependiam de configurações específicas de sistema operacional, bibliotecas e ambientes. Containers não eram uma novidade — tecnologias como **chroot**, **LXC** (_Linux Containers_), e **[[cgroups]]** já existiam no Linux, mas eram difíceis de usar e configurar.
    
2. **A Fundação: dotCloud (2010)**  
    Em 2010, **Solomon Hykes** fundou uma startup chamada **dotCloud**. O objetivo era oferecer uma plataforma como serviço (_Platform-as-a-Service_, ou PaaS), semelhante ao Heroku. Durante o desenvolvimento da plataforma, Hykes e sua equipe criaram uma ferramenta interna para empacotar e isolar aplicativos, que mais tarde se tornaria o Docker.
    
3. **O Lançamento do Docker (2013)**  
    Em março de 2013, Solomon Hykes apresentou o **Docker** na conferência PyCon. O Docker era uma abstração simples e poderosa sobre tecnologias existentes como **[[namespaces]]** e **[[cgroups]]**, permitindo que desenvolvedores criassem containers facilmente. Ele introduziu **[[imagens]] Docker** (para empacotar aplicativos com todas as suas dependências), o **Docker Engine** (runtime leve para rodar containers) e **portabilidade** (um container podia rodar em qualquer lugar, de um laptop a um servidor na nuvem).
    
4. **Crescimento e Adoção (2014-2016)**  
    O Docker rapidamente ganhou tração na comunidade de desenvolvedores, pois simplificava a implantação de aplicativos. A **Docker Inc.** foi fundada para gerenciar o projeto e a marca Docker. Grandes empresas como Google, Red Hat, IBM e AWS começaram a integrar o Docker em seus produtos.
    
5. **O Ecosistema Docker**  
    O Docker evoluiu de uma ferramenta simples para um ecossistema completo de containers, incluindo o **[[Docker Hub]]** (repositório de imagens Docker), **Docker [[Compose]]** (orquestração de múltiplos containers) e **[[Docker Swarm]]** (orquestração de containers nativa).
    
6. **A Ascensão do [[Kubernetes]] (2016-2018)**  
    Em 2014, o **[[Kubernetes]]** (criado pelo Google) começou a ganhar destaque como uma ferramenta para orquestrar containers em grande escala. Ele superou o Docker Swarm em popularidade, mas o Docker continuou sendo a base para rodar containers dentro do Kubernetes.
    
7. **Contêineres Tornam-se Mainstream**  
    O Docker mudou a forma como o software é desenvolvido, implantado e executado. Ele tornou **DevOps** mais eficiente, acelerou o surgimento de arquiteturas de **microservices** e popularizou conceitos como **infraestrutura como código** e **CI/CD**.
    
8. **O Futuro: Docker e Open Source**  
    Em 2020, a **Mirantis** adquiriu a divisão de Docker Enterprise, enquanto a Docker Inc. focou na experiência do desenvolvedor e ferramentas open-source. O Docker ainda é amplamente usado por desenvolvedores para construir, testar e compartilhar aplicações, enquanto tecnologias como [[Kubernetes]] gerenciam containers em escala.

O Docker foi criado em **[[Go]]** ([[Go]]), uma linguagem de programação desenvolvida pelo Google. Go foi escolhido por sua combinação de **desempenho**, **eficiência** e **facilidade de uso**, características ideais para o tipo de aplicação que o Docker precisava ser.

### **Por que Go foi escolhido?**

1. **Concorrência**: Go tem suporte nativo para programação concorrente com [[Goroutine]] e [[channels]], o que é essencial para gerenciar múltiplos containers de forma eficiente.
2. **Desempenho**: Embora não seja tão rápida quanto C ou C++, Go é significativamente mais rápida que linguagens interpretadas como Python e Ruby.
3. **Facilidade de Manutenção**: Go tem uma sintaxe simples e ferramentas integradas (como o `go fmt` para formatação e `go build` para compilação), facilitando o desenvolvimento e manutenção do código.
4. **Compilação Estática**: O Docker precisava ser uma aplicação que pudesse rodar em qualquer lugar sem dependências externas, algo que Go facilita com seus binários estáticos.

Portanto, o Docker é essencialmente um exemplo perfeito de como Go pode ser usado para criar aplicações robustas, eficientes e portáveis.

# Comandos do Docker

## Iniciar um container

O comando `docker run` é um dos mais utilizados no [[Docker]] e serve para criar e executar contêineres a partir de imagens. Ele combina a funcionalidade de criar o [container](Container.md) (`docker create`) com a de iniciar sua execução (`docker start`).

Aqui estão os **argumentos mais utilizados** no `docker run` e suas funções:

### **1. `--name`**

- **Descrição**: Dá um nome ao contêiner.
- **Exemplo**: `docker run --name meu-container nginx`
- **Uso**: Facilita a identificação e manipulação do contêiner posteriormente.

### **2. `-d` ou `--detach`**

- **Descrição**: Executa o contêiner em segundo plano (modo "desacoplado").
- **Exemplo**: `docker run -d nginx`
- **Uso**: Útil para serviços que precisam rodar continuamente, como servidores web.

### **3. `-p` ou `--publish`**

- **Descrição**: Faz o mapeamento de portas entre o host e o contêiner.
- **Exemplo**: `docker run -p 8080:80 nginx`
- **Uso**: Permite acessar aplicações rodando no contêiner a partir do host.

### **4. `-v` ou `--volume`**

- **Descrição**: Monta volumes (ou diretórios) do host dentro do contêiner.
- **Exemplo**: `docker run -v /meu/diretorio:/app nginx`
- **Uso**: Útil para persistência de dados e desenvolvimento local.

### **5. `-e` ou `--env`**

- **Descrição**: Define variáveis de ambiente para o contêiner.
- **Exemplo**: `docker run -e APP_ENV=production nginx`
- **Uso**: Passa configurações dinâmicas para o contêiner.

### **6. `--rm`**

- **Descrição**: Remove automaticamente o contêiner ao finalizar sua execução.
- **Exemplo**: `docker run --rm nginx`
- **Uso**: Ideal para contêineres temporários ou de testes.

### **7. `--network`**

- **Descrição**: Define a rede que o contêiner deve usar.
- **Exemplo**: `docker run --network minha-rede nginx`
- **Uso**: Necessário para comunicar múltiplos contêineres entre si.

### **8. `--restart`**

- **Descrição**: Configura a política de reinício do contêiner.
- **Exemplo**: `docker run --restart unless-stopped nginx`
- **Opções**:
    - `no`: Não reinicia (padrão).
    - `always`: Sempre reinicia.
    - `unless-stopped`: Reinicia, exceto se o contêiner for parado manualmente.

### **9. `-it`**

- **Descrição**: Permite interatividade com o terminal do contêiner.
- **Exemplo**: `docker run -it ubuntu bash`
- **Uso**: Útil para debugging e execução de comandos interativos.

### **10. `--cpus` e `--memory`**

- **Descrição**: Define limites de CPU e memória para o contêiner.
- **Exemplo**:
    - `docker run --cpus="1.5" nginx`
    - `docker run --memory="512m" nginx`
- **Uso**: Garante que o contêiner não consuma mais recursos do que o permitido.

## **Parar um contêiner**

- **Comando**:

    ```bash
docker stop <nome_do_container> ou <id_do_container>
```

- **Descrição**: Interrompe a execução de um contêiner ativo de forma segura.
- **Exemplo**:

    ```bash
docker stop meu-container
```


## **Subir novamente um contêiner parado**

- **Comando**:

    ```bash
docker start <nome_do_container> ou <id_do_container>
```

- **Descrição**: Reinicia um contêiner que foi parado anteriormente.
- **Exemplo**:

    ```bash
docker start meu-container
```

## **Listar todos os contêineres (ativos e inativos)**

- **Comando**:

    ```bash
docker ps -a
```

- **Descrição**: Mostra todos os contêineres, incluindo os que estão parados.
- **Exemplo de saída**:

    ```plaintext
    CONTAINER ID   IMAGE        STATUS              NAMES
    a1b2c3d4e5     nginx        Up 5 minutes        web-server
    f6g7h8i9j0     ubuntu       Exited (0) 1 hour   teste-ubuntu
    ```