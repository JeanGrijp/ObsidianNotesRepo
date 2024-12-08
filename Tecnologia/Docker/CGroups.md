---
tags:
  - Tecnologia
  - Docker
  - Linux
  - Kernel
---
O **cgroups** (abreviação de _control groups_) no Linux é como um **controle remoto para recursos do sistema**.

Ele serve para:

1. **Definir limites**: Você pode dizer, por exemplo, "Esse processo só pode usar até 1 GB de memória" ou "Essa aplicação pode usar no máximo 50% da CPU".
2. **Priorizar recursos**: Alguns processos podem ter prioridade no uso da CPU ou da memória, garantindo que eles sejam mais rápidos.
3. **Monitorar consumo**: Você pode observar quanto de recurso cada processo está usando.
4. **Isolar recursos**: Ele separa os recursos entre diferentes processos ou grupos, evitando que um processo consuma tudo e trave o sistema.

Pense no cgroups como uma caixa de ferramentas que:

- Define **quanta comida (recursos)** cada "pessoa" (processo) pode pegar.
- Garante que ninguém roube comida dos outros.
- E verifica quem está comendo mais ou menos.

No contexto do **Docker**, o **cgroups** (control groups) é uma peça-chave que permite que cada container tenha **controle rígido sobre os recursos que pode usar**, garantindo isolamento e estabilidade. Vamos simplificar:

### **Como o cgroups funciona no Docker?**

1. **Limitação de Recursos**:
    
    - Quando você cria ou configura um container, pode dizer coisas como:
        - "Esse container pode usar no máximo 2 CPUs."
        - "Esse container tem no máximo 512 MB de RAM."
        - "Limite a velocidade de gravação em disco a 20 MB/s."
    - O Docker usa **cgroups** para aplicar essas regras. Assim, mesmo que um container tente consumir mais recursos, o cgroups corta o acesso, mantendo o limite definido.
2. **Isolamento de Recursos**:
    
    - Com **cgroups**, o Docker garante que um container não monopolize recursos, prejudicando o desempenho dos outros containers ou do sistema.
    - Exemplo: Sem cgroups, um container faminto por CPU poderia deixar todo o servidor lento. Com cgroups, o uso da CPU é limitado por container.
3. **Monitoramento**:
    
    - O Docker pode monitorar quanto de CPU, memória, rede ou disco cada [[container]] está usando em tempo real, graças ao **cgroups**.
    - Isso é útil para verificar gargalos ou containers problemáticos.

---

### **Exemplo prático no Docker**

Se você rodar um comando como:

```bash
docker run --memory=512m --cpus=1 my-container
```

Isso faz com que o Docker crie um **grupo de controle (cgroup)** para o container `my-container`, dizendo:

- **Memória**: Pode usar no máximo 512 MB.
- **CPU**: Pode usar no máximo 1 núcleo de CPU.

Esses limites são gerenciados pelo kernel do Linux usando o **cgroups**.

---

### **Por que isso é importante?**

- **Performance estável**: Mesmo que um container esteja sobrecarregado, ele não prejudica o desempenho dos outros.
- **Eficiência**: Você pode otimizar o uso do servidor, alocando os recursos de forma precisa para cada container.
- **Escalabilidade**: É fácil rodar múltiplos containers com recursos controlados, sem medo de conflitos.