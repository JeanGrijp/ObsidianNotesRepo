O `pprof` é uma ferramenta integrada no runtime do [[Go]] que permite **perfilamento de desempenho** (profiling) para identificar gargalos e otimizar o uso de recursos como CPU, memória e [[Goroutine]]. Ele é essencial para diagnósticos e melhoria de desempenho em aplicações Go.

---

### **Como Funciona o pprof**

1. **Perfilamento**:
    
    - O `pprof` coleta informações detalhadas sobre a execução de um programa Go, como:
        - Uso de CPU.
        - Alocação de memória.
        - Contagem de goroutines.
        - Tempo gasto em funções específicas.
2. **Visualização**:
    
    - Os dados coletados podem ser analisados diretamente no terminal ou usando ferramentas como o **`go tool pprof`**, que oferece relatórios detalhados e gráficos interativos.
3. **Tipos de Perfis Disponíveis**:
    
    - **CPU**: Mostra o tempo de CPU gasto em cada função.
    - **Memória**: Exibe as alocações de memória na heap.
    - **Goroutines**: Monitora o número e o estado das goroutines.
    - **Bloqueios**: Identifica onde goroutines estão sendo bloqueadas.
    - **Heap**: Detalha o uso de memória alocada.

---

### **Habilitando pprof**

1. **Importar o pacote de profiling**:
    
    - Para habilitar o `pprof`, importe o pacote `net/http/pprof` no seu programa:
        
        ```go
        import _ "net/http/pprof"
        ```
        
2. **Expor um servidor [[HTTP]] para acessar os dados de profiling**:
    
    - Adicione o servidor [[HTTP]] para expor as métricas:
        
        ```go
        package main
        
        import (
            "net/http"
            _ "net/http/pprof"
        )
        
        func main() {
            go func() {
                // Servidor HTTP para pprof
                http.ListenAndServe("localhost:6060", nil)
            }()
        
            // Sua aplicação aqui
            select {}
        }
        ```
        
3. **Executar a aplicação**:
    
    - Ao executar o programa, o `pprof` estará disponível no endereço `http://localhost:6060/debug/pprof/`.

---

### **Comandos Principais do pprof**

Após habilitar o `pprof`, você pode coletar e analisar dados usando o comando:

```sh
go tool pprof
```

#### Exemplo: Perfil de CPU

1. **Coletar o perfil de CPU**:
    
    ```sh
    go tool pprof http://localhost:6060/debug/pprof/profile?seconds=30
    ```
    
    - Isso coleta dados de CPU durante 30 segundos.
2. **Abrir o relatório interativo**:
    
    - Depois de coletar os dados, o terminal exibe um relatório interativo:
        
        ```sh
        (pprof) top
        ```
        
        - Mostra as funções que mais consumiram CPU.

#### Outros Perfis:

- **[[Heap]]**:
    
    ```sh
    go tool pprof http://localhost:6060/debug/pprof/heap
    ```
    
    - Mostra alocações de memória na heap.
- **[[Goroutine]]**:
    
    ```sh
    go tool pprof http://localhost:6060/debug/pprof/goroutine
    ```
    
    - Exibe o número e o estado das goroutines.

---

### **Visualizando os Dados**

1. **Relatórios Interativos**:
    
    - Comandos úteis no terminal:
        - `top`: Mostra as funções que mais consomem recursos.
        - `list funcName`: Mostra o código fonte da função `funcName` e quanto recurso ela consome.
        - `callgraph`: Mostra um gráfico de chamadas.
2. **Gráficos Visuais**:
    
    - Instale o **[[Graphviz]]** para gerar gráficos visuais.
    - Salve o perfil em um arquivo e use o comando:
        
        ```sh
        go tool pprof -http=:8080 profile.out
        ```
        
    - Isso abre uma interface gráfica no navegador para análise.

---

### **Melhores Práticas com pprof**

1. **Focar no Perfil Correto**:
    
    - Use o perfil de CPU para desempenho, o de heap para problemas de memória, e o de goroutines para concorrência.
2. **Testar em Ambiente de Produção**:
    
    - Colete perfis no ambiente real, mas evite habilitar o `pprof` continuamente em produção por razões de desempenho e segurança.
3. **Ajustar o Garbage Collector**:
    
    - Combine `pprof` com ajustes no comportamento do GC (variável `GOGC`) para identificar e corrigir problemas de alocação de memória.
4. **Analisar Goroutines**:
    
    - Use o perfil de goroutines para detectar vazamentos ou goroutines bloqueadas.

---

### **Exemplo de Análise Completa**

```go
package main

import (
    "fmt"
    "net/http"
    _ "net/http/pprof"
    "time"
)

func leakyFunction() {
    for {
        _ = make([]byte, 1<<20) // Aloca 1 MB por loop
        time.Sleep(100 * time.Millisecond)
    }
}

func main() {
    go func() {
        http.ListenAndServe("localhost:6060", nil) // Servidor pprof
    }()

    go leakyFunction()

    fmt.Println("Application running. Access pprof at http://localhost:6060/debug/pprof/")
    select {}
}
```

**Passos para Análise**:

1. Execute o programa.
2. Acesse o perfil de heap:
    
    ```sh
    go tool pprof http://localhost:6060/debug/pprof/heap
    ```
    
3. Use o comando `top` ou visualize no navegador para identificar o problema (alocações excessivas na função `leakyFunction`).

---

### **Conclusão**

O `pprof` é uma ferramenta poderosa para otimizar aplicações Go, fornecendo insights sobre uso de CPU, memória, e goroutines. Quando bem utilizado, ele permite identificar gargalos e melhorar o desempenho do sistema de forma significativa.