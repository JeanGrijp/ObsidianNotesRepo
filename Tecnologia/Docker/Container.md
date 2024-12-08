---
tags:
  - Tecnologia
  - Docker
---
Containers são ambientes isolados no [[docker]], eles guardam toda a configuração que a aplicação precisa para ser executada. De forma técnica, um **container** em Docker é uma **instância leve e isolada** que roda uma aplicação ou serviço em um ambiente padronizado, utilizando tecnologias do kernel do Linux, como **[[namespaces]]** e **[[cgroups]]**, para fornecer isolamento de processos, recursos e segurança.

---

### **Funcionamento Técnico de um Container Docker**

1. **Imagens como Base**
    
    - Um container é baseado em uma **imagem Docker**, que é um arquivo imutável contendo:
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

---

### **Resumo Técnico**

- Um container Docker é uma **instância de uma imagem**, que roda como um **processo isolado** no host.
- Ele utiliza:
    - **Namespaces** para isolamento de processos, rede, e sistema de arquivos.
    - **Cgroups** para limitar o uso de recursos.
    - **OverlayFS** para gerenciar o sistema de arquivos leve e compartilhado.
- Compartilha o kernel do host, diferentemente de máquinas virtuais, mas ainda mantém um ambiente isolado para executar aplicações.

---

Essa abordagem técnica garante que os containers sejam:

- **Leves**: Sem overhead de kernel adicional.
- **Eficientes**: Compartilhando recursos sem interferência.
- **Seguros**: Com isolamento garantido pelo kernel.