---
tags:
  - Tecnologia
  - Docker
---
Todos os [[Container]] em docker tem como base o princípio de isolamento.

![](Captura%20de%20tela%202024-12-07%20190003%201.png)

Essa imagem explica como o Docker implementa o isolamento entre containers utilizando duas tecnologias principais do kernel do Linux: **namespaces** e **cgroups**. Aqui está a explicação:

### **1. Host e Kernel**

- O kernel do sistema operacional é compartilhado entre todos os containers e o host, mas cada container parece estar rodando em seu próprio sistema isolado devido ao uso das tecnologias abaixo.

---

### **2. Namespaces**

- **O que são?** Namespaces criam ambientes separados para cada container. É como dar a cada container uma "visão" única e isolada do sistema operacional, sem interferir nos outros.
- **Como funciona?** Cada container tem seu próprio espaço para recursos como:
    - Sistema de arquivos (mount namespace)
    - Identidade de processos (PID namespace)
    - Rede (network namespace)
    - Identificadores de usuários (user namespace)
    - Interprocess Communication (IPC namespace)

**Resultado:** Cada container pensa que tem acesso exclusivo a esses recursos, mas na verdade são instâncias isoladas dentro do sistema.

---

### **3. Cgroups (Control Groups)**

- **O que são?** Cgroups são usados para limitar e monitorar o uso de recursos físicos como CPU, memória, I/O de disco e rede.
- **Como funciona?** Cada container tem um grupo de controle específico que define:
    - Quanto de CPU ele pode usar.
    - Quanta memória ele pode consumir.
    - Qual o limite de leitura/escrita no disco.

**Resultado:** Garante que um container não monopolize os recursos do sistema, afetando outros containers.

---

### **4. Combinação de Namespaces e Cgroups**

- O isolamento é obtido ao usar namespaces para separar o ambiente de execução e cgroups para gerenciar os recursos físicos.
- Essa combinação permite rodar múltiplos containers no mesmo host sem que eles interfiram uns nos outros.

---

**Conclusão:**  
A imagem mostra que:

- Cada container opera em um ambiente isolado (namespaces).
- Os recursos físicos usados por cada container são controlados pelo kernel via cgroups.
- Tudo isso é gerenciado no mesmo sistema operacional do host, sem a necessidade de máquinas virtuais.