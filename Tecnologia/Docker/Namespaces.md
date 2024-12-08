---
tags:
  - Tecnologia
  - Docker
  - Linux
---
De forma técnica, **namespaces** no Linux são uma funcionalidade do [[kernel]] que cria um ambiente isolado para recursos específicos, permitindo que processos tenham sua própria visão e controle sobre eles. Essa é a base do isolamento de processos em [Containers](Container.md), como os usados pelo [[Docker]].

Em outras palavras, **namespaces** separam os recursos de um processo ou grupo de processos para que eles pensem que são os únicos que existem no sistema.

### **Tipos de Namespaces e Seus Propósitos**

1. **PID (Process ID Namespace)**:
    
    - Isola os IDs de processos.
    - Cada namespace tem seu próprio conjunto de IDs de processos.
    - Exemplo: Um container pode ver o processo "1" como seu processo principal, enquanto no host esse mesmo processo tem um ID diferente.
2. **Mount (Mount Namespace)**:
    
    - Isola os pontos de montagem de sistemas de arquivos.
    - Permite que cada namespace tenha sua própria hierarquia de sistemas de arquivos.
    - Exemplo: Um container pode montar seus próprios diretórios e não enxergar os diretórios do host ou de outros containers.
3. **Net (Network Namespace)**:
    
    - Isola interfaces de rede, tabelas de roteamento e portas.
    - Cada container pode ter sua própria interface de rede virtual (veth) e endereço IP.
    - Exemplo: Um container pode ter acesso exclusivo a uma interface de rede sem saber da existência de outras.
4. **IPC (Inter-Process Communication Namespace)**:
    
    - Isola os mecanismos de comunicação entre processos, como filas de mensagens e semáforos.
    - Exemplo: Um container pode ter seu próprio segmento de memória compartilhada que não interfere em outros containers.
5. **UTS (UNIX Time Sharing Namespace)**:
    
    - Isola os nomes de host e de domínio.
    - Exemplo: Um container pode ter seu próprio nome de host diferente do nome do host físico.
6. **User Namespace**:
    
    - Isola os IDs de usuários e grupos.
    - Permite que processos dentro de um namespace tenham IDs de usuários diferentes dos IDs no host.
    - Exemplo: Um processo pode rodar como "root" dentro do container, mas ser mapeado para um ID de usuário não privilegiado no host.
7. **Cgroup Namespace**:
    
    - Isola a visualização de cgroups para processos.
    - Exemplo: Um container pode "enxergar" apenas os limites e configurações de cgroup aplicados a ele.

### **Como Namespaces Funcionam no Docker**

- **Criação de Ambientes Isolados**: Quando você inicia um container, o Docker usa namespaces para criar um ambiente isolado, garantindo que os processos dentro do container não possam acessar recursos do host ou de outros containers.
- **Customização**: Cada container pode ser configurado para usar namespaces diferentes, dependendo do nível de isolamento necessário.

### **Exemplo Prático**

Imagine que você tem dois containers no mesmo host:

- Cada container pode ter:
    - Seus próprios processos (PID namespace).
    - Seus próprios arquivos e diretórios (Mount namespace).
    - Seus próprios IPs e portas (Network namespace).
- Eles não sabem da existência um do outro, mesmo compartilhando o mesmo kernel.