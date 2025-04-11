Um **memory leak** ocorre quando um programa reserva (aloca) memória, mas falha em liberá-la, tornando-a inacessível, mesmo que não esteja mais em uso. Essa memória perdida permanece ocupada, o que pode levar a uma degradação de desempenho ou até ao esgotamento da memória disponível.

---

### **Como os Memory Leaks Acontecem?**

1. **Referências Persistentes**:
    
    - Objetos ou variáveis ainda têm referências, mas não são mais úteis.
    - O [[Garbage Collector]] não pode liberar essa memória porque acredita que ela está sendo usada.
    - Exemplo:
        
        ```go
        var global = make([]int, 1_000_000) // 'global' mantém a memória viva, mesmo se não for usada.
        ```
        
2. **Ciclos de Referência**:
    
    - Objetos referenciam uns aos outros, formando um ciclo que impede o GC de identificá-los como inacessíveis.
    - Em algumas linguagens, como C ou Python, isso é uma causa comum de vazamentos. Em [[Go]], o Garbage Collector consegue resolver ciclos de referência, minimizando esse problema.
3. **Recursos Externos Não Liberados**:
    
    - Recursos alocados fora da [[Heap]], como arquivos, conexões de rede ou handles de sistema operacional, não são liberados corretamente.
    - Em Go, isso ocorre quando não se usa `defer` ou funções adequadas para liberar recursos.
    
    Exemplo de problema:
    
    ```go
    func leak() {
        file, _ := os.Open("example.txt")
        // 'file' nunca é fechado
    }
    ```
    
4. **Acúmulo de Dados Não Necessários**:
    
    - Estruturas de dados como `map` ou `slice` crescem desnecessariamente.
    - Exemplo:
        
        ```go
        func accumulate() {
            data := make([]int, 0)
            for i := 0; i < 1_000_000; i++ {
                data = append(data, i) // Dados nunca são limpos
            }
        }
        ```
        

---

### **Impactos dos Memory Leaks**

1. **Esgotamento da Memória**:
    
    - O programa consome toda a memória disponível, causando travamentos.
2. **Degradação de Desempenho**:
    
    - À medida que a memória se esgota, o sistema pode se tornar lento devido ao aumento de [[Swaps]] (uso de memória secundária) ou maior carga no Garbage Collector.
3. **Imprevisibilidade**:
    
    - O comportamento do programa pode variar dependendo de quanta memória está disponível no sistema.

---

### **Como Evitar Memory Leaks em Geral**

1. **Gerenciar Referências**:
    
    - Remova referências a objetos desnecessários, permitindo que o Garbage Collector libere memória.
2. **Liberar Recursos Externos**:
    
    - Sempre libere recursos como arquivos e conexões, usando `defer` em Go para garantir o fechamento.
    - Exemplo:
        
        ```go
        func processFile() {
            file, err := os.Open("example.txt")
            if err != nil {
                return
            }
            defer file.Close()
            // Processa o arquivo
        }
        ```
        
3. **Limpar Estruturas Dinâmicas**:
    
    - Remova dados de estruturas como mapas e slices que não são mais necessários.
    - Exemplo:
        
        ```go
        func clearSlice() {
            slice := make([]int, 10)
            // Remove os dados
            slice = nil
        }
        ```
        
4. **Analisar Ciclos de Referência**:
    
    - Em linguagens onde o GC não resolve ciclos, elimine referências circulares manualmente.
5. **Ferramentas de Diagnóstico**:
    
    - Use ferramentas para detectar vazamentos, como _[[PProf]]_ em Go, ou analisadores de heap.

---

### **Memory Leaks em Go**

Embora Go tenha um Garbage Collector eficiente, **memory leaks ainda podem acontecer**, geralmente devido a:

1. **Referências Persistentes**:
    
    - Manter referências em estruturas globais ou estáticas desnecessariamente impede o GC de liberar a memória.
2. **Acúmulo em Goroutines**:
    
    - Goroutines que nunca terminam ou ficam aguardando dados de canais bloqueados podem reter memória.
    - Exemplo de vazamento em goroutines:
        
        ```go
        func leakGoroutine() {
            ch := make(chan int)
            go func() {
                for v := range ch { // Nunca fecha
                    fmt.Println(v)
                }
            }()
        }
        ```
        
3. **Recursos Externos Não Gerenciados**:
    
    - Arquivos ou conexões não fechados corretamente.
4. **Excesso de Dados em Estruturas Dinâmicas**:
    
    - Mapas ou slices que crescem sem necessidade.

---

### **Ferramentas para Detectar e Prevenir Leaks em Go**

1. **pprof**:
    
    - Ferramenta integrada ao Go para análise de desempenho e uso de memória.
    - Permite identificar quais partes do código estão consumindo mais memória.
    
    Exemplo:
    
    ```go
    import _ "net/http/pprof"
    ```
    
    Depois, use um navegador para acessar:
    
    ```
    http://localhost:6060/debug/pprof/
    ```
    
2. **GOGC**:
    
    - Ajuste o comportamento do Garbage Collector para encontrar problemas relacionados a alta pressão de memória.

---

### **Como Evitar Memory Leaks em Go**

1. **Feche Recursos Externos**:
    
    - Sempre use `defer` para fechar arquivos, conexões ou canais.
    - Exemplo:
        
        ```go
        func useFile() {
            file, _ := os.Open("example.txt")
            defer file.Close()
            // Trabalhar com o arquivo
        }
        ```
        
2. **Evite Goroutines Não Finalizadas**:
    
    - Certifique-se de que todas as goroutines terminem adequadamente ou sejam controladas por canais e contextos.
    - Exemplo:
        
        ```go
        func process(ch chan int) {
            defer close(ch)
            for i := 0; i < 10; i++ {
                ch <- i
            }
        }
        ```
        
3. **Remova Dados Inúteis**:
    
    - Limpe mapas, slices ou estruturas de dados que não são mais necessários.
    - Exemplo:
        
        ```go
        func clearMap() {
            m := map[string]int{"key": 1}
            delete(m, "key") // Remove a entrada do mapa
        }
        ```
        
4. **Monitore Goroutines**:
    
    - Use ferramentas como `runtime.NumGoroutine` para identificar vazamentos de goroutines.

---

### **Resumo e Relação com Go**

Go reduz significativamente a probabilidade de vazamentos de memória graças ao seu Garbage Collector, que:

- Libera automaticamente objetos na heap que não têm referências.
- Resolve ciclos de referência.
- Permite baixa latência para sistemas de alta performance.

No entanto, **memory leaks ainda podem ocorrer em Go** devido a:

1. Recursos externos não fechados.
2. Goroutines que não finalizam.
3. Estruturas de dados não limpas.

Para evitá-los, siga boas práticas, use ferramentas de monitoramento como `pprof`, e aplique um gerenciamento cuidadoso de goroutines e recursos externos.