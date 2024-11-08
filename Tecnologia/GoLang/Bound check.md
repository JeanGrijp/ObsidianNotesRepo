---
tags:
  - Tecnologia
  - GoLang
---
Em Go, a verificação de limites (*bound check*) é feita automaticamente pelo compilador para garantir que o acesso a elementos de um *slice* ou *array* seja seguro. 

### Como Funciona a Verificação de Limite

1. **Durante a Execução**: Quando você acessa um elemento de um *slice* ou *array*, o compilador gera código que verifica se o índice está dentro dos limites válidos (entre `0` e `len(slice) - 1`).
2. **Segurança de Memória**: Se o índice estiver fora desse intervalo, o programa dispara um *runtime panic*, evitando acessos inseguros que poderiam corromper a memória.

### Exemplo Simples

```go
func main() {
    meuSlice := []int{10, 20, 30, 40, 50}
    _ = meuSlice[3] // Acessa o elemento no índice 3
}
```

### O Que Acontece Aqui?

- Quando você faz `meuSlice[3]`, o compilador insere uma verificação implícita para confirmar que `3` está dentro do intervalo válido.
- Se o índice fosse, por exemplo, `5` (maior ou igual ao tamanho do *slice*), o programa geraria um *panic* de tempo de execução como:
  ```
  panic: runtime error: index out of range [5] with length 5
  ```

### Otimizações do Compilador

- O compilador de Go é inteligente e, em muitos casos, consegue otimizar essas verificações, especialmente quando consegue provar que o índice estará sempre dentro dos limites (por exemplo, em loops ou casos triviais).
- Em situações onde a verificação é redundante ou desnecessária, o compilador pode eliminá-la, melhorando o desempenho.

### Por Que Fazer Isso?

A verificação automática de limites é uma das razões pelas quais Go é uma linguagem segura quanto a erros de memória, evitando vulnerabilidades comuns em linguagens como C e C++, onde você pode acessar qualquer posição de memória se não houver cuidado.