---
tags:
  - Tecnologia
  - GoLang
---
O **Garbage Collector (GC)** é um mecanismo automático em Go que gerencia a memória alocada dinamicamente na **[[heap]]**, liberando espaço que não está mais sendo usado pelo programa. Isso evita vazamentos de memória (_[[memory leaks]]_) e facilita o desenvolvimento, eliminando a necessidade de gerenciar a liberação manual de memória.

---

### **Como Funciona o Garbage Collector em Go?**

1. **Identificação de Objetos Inacessíveis**:
    
    - O GC rastreia todos os objetos na heap e verifica quais ainda podem ser acessados diretamente ou indiretamente pelas variáveis em uso.
    - Objetos inacessíveis (sem referências) são considerados "órfãos" e marcados para liberação.
2. **Coleta de Memória**:
    
    - Após identificar os objetos órfãos, o GC libera a memória associada a eles.
    - Isso acontece em ciclos periódicos e não requer intervenção do desenvolvedor.
3. **Parada do Mundo (Stop-the-world)**:
    
    - Durante certos estágios da coleta, o GC pode pausar temporariamente a execução do programa para garantir consistência. Em Go, isso é otimizado para minimizar o impacto no desempenho.

---

### **Características do Garbage Collector em Go**

1. **Gerenciado Automático**:
    
    - O desenvolvedor não precisa liberar a memória manualmente, como seria necessário em linguagens como C/C++.
    - Em Go, o GC é ativado automaticamente pelo runtime.
2. **Baixa Latência**:
    
    - O Garbage Collector do Go foi projetado para aplicações que exigem baixa latência, como sistemas de alta performance.
    - Ele realiza a coleta de memória de forma incremental e concorrente, minimizando interrupções.
3. **Compactação**:
    
    - Após liberar a memória, o GC pode organizar os dados na heap para reduzir fragmentação.

---

### **Etapas do Garbage Collector**

1. **Marcar (Mark)**:
    
    - O GC identifica todos os objetos acessíveis a partir de variáveis ativas e suas referências.
    - Esses objetos são "marcados" como ainda em uso.
2. **Varredura (Sweep)**:
    
    - Objetos que não foram marcados são considerados lixo e a memória associada é liberada.
3. **Compactação (Opcional)**:
    
    - O GC pode reorganizar os objetos na heap para evitar fragmentação, dependendo da implementação.

---

### **Exemplo Prático**

```go
package main

import "fmt"

type Data struct {
    Value int
}

func createData() *Data {
    d := &Data{Value: 42} // Alocado na heap
    return d
}

func main() {
    data := createData() // Referência ao objeto na heap
    fmt.Println(data.Value)
    data = nil           // Remove a referência
    // O GC irá liberar o espaço ocupado por 'Data'
}
```

**O que acontece aqui**:

1. O objeto `Data` é criado na heap.
2. `data` mantém uma referência ao objeto.
3. Quando `data = nil`, o objeto não tem mais referências e será coletado pelo GC em um ciclo futuro.

---

### **Vantagens do Garbage Collector**

1. **Facilidade de Uso**:
    
    - O desenvolvedor não precisa se preocupar com a liberação de memória manual, reduzindo bugs como vazamentos de memória e referências inválidas.
2. **Segurança**:
    
    - Evita problemas como **double-free** (tentar liberar a mesma memória duas vezes) e **dangling pointers** (ponteiros para memória já liberada).
3. **Otimização do Tempo de Vida**:
    
    - Libera memória automaticamente quando não está mais sendo usada, melhorando o gerenciamento geral.

---

### **Desvantagens do Garbage Collector**

1. **Impacto no Desempenho**:
    
    - Apesar de otimizado, o GC consome recursos de CPU durante seus ciclos.
    - Programas sensíveis à latência podem sentir um impacto durante a execução do GC.
2. **Controle Limitado**:
    
    - O desenvolvedor não tem controle direto sobre quando a coleta acontece, apenas pode ajustar configurações para influenciar o comportamento.

---

### **Como o GC Decide Limpar a Memória?**

- O **runtime** de Go monitora o uso da heap.
- Quando a memória usada atinge um certo limite ou quando há pressão de memória no sistema, o GC é ativado.
- O desenvolvedor pode configurar o comportamento do GC usando a variável de ambiente `GOGC`:
    - **Padrão (100)**: O GC será ativado quando a memória usada pela heap duplicar.
    - **Aumentar GOGC**: Reduz a frequência do GC (melhor performance, mas maior uso de memória).
    - **Diminuir GOGC**: Aumenta a frequência do GC (menor uso de memória, mas maior impacto no desempenho).

Exemplo:

```sh
GOGC=50 go run main.go
```

---

### **Quando a Memória é Liberada?**

A memória é liberada quando:

1. O objeto não tem mais referências acessíveis.
2. O Garbage Collector executa um ciclo de coleta.
3. Não há ciclos de referência (em Go, o GC pode lidar com isso).

---

### **Conclusão**

O Garbage Collector em Go é uma ferramenta poderosa que gerencia automaticamente a memória na heap, garantindo que os objetos não usados sejam removidos e o espaço seja liberado. Ele oferece simplicidade e segurança, mas pode impactar o desempenho em certos cenários. O desenvolvedor pode otimizar o uso da memória com boas práticas, mas não precisa se preocupar com a liberação manual de memória.