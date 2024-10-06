Em C# .NET ([Dotnet](Dotnet.md)), os arrays também possuem uma variedade de métodos e propriedades úteis para manipulação e gerenciamento de dados. Aqui estão alguns dos principais métodos e propriedades para trabalhar com arrays:

### **Propriedades do Array**

1. **`Length`**: Retorna o número de elementos no array.
   ```csharp
   int[] numeros = { 1, 2, 3, 4, 5 };
   int tamanho = numeros.Length; // Retorna 5
   ```

2. **`Rank`**: Retorna o número de dimensões do array.
   ```csharp
   int[,] matriz = new int[3, 5];
   int dimensoes = matriz.Rank; // Retorna 2 (porque é uma matriz 2D)
   ```

3. **`IsReadOnly`**: Verifica se o array é somente leitura.
   ```csharp
   int[] numeros = { 1, 2, 3 };
   bool isReadOnly = numeros.IsReadOnly; // Retorna false
   ```

### **Métodos do Array**

1. **`Array.Sort()`**: Ordena os elementos de um array.
   ```csharp
   int[] numeros = { 5, 3, 8, 1 };
   Array.Sort(numeros); // Ordena o array: [1, 3, 5, 8]
   ```

2. **`Array.Reverse()`**: Inverte a ordem dos elementos do array.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   Array.Reverse(numeros); // Inverte o array: [4, 3, 2, 1]
   ```

3. **`Array.IndexOf()`**: Retorna o índice da primeira ocorrência de um valor específico.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int indice = Array.IndexOf(numeros, 3); // Retorna 2
   ```

4. **`Array.LastIndexOf()`**: Retorna o índice da última ocorrência de um valor específico.
   ```csharp
   int[] numeros = { 1, 2, 3, 2, 4 };
   int indice = Array.LastIndexOf(numeros, 2); // Retorna 3
   ```

5. **`Array.Clear()`**: Remove todos os elementos de um array, definindo-os como o valor padrão para o tipo.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   Array.Clear(numeros, 0, numeros.Length); // Limpa todos os elementos (tudo vira 0)
   ```

6. **`Array.Copy()`**: Copia os elementos de um array para outro.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int[] copia = new int[4];
   Array.Copy(numeros, copia, numeros.Length); // Copia os elementos de "numeros" para "copia"
   ```

7. **`Array.Exists()`**: Verifica se algum elemento do array atende a uma condição especificada.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   bool existe = Array.Exists(numeros, element => element == 3); // Retorna true
   ```

8. **`Array.Find()`**: Retorna o primeiro elemento que atende a uma condição.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int encontrado = Array.Find(numeros, element => element > 2); // Retorna 3
   ```

9. **`Array.FindAll()`**: Retorna um array contendo todos os elementos que atendem a uma condição.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int[] encontrados = Array.FindAll(numeros, element => element > 2); // Retorna [3, 4]
   ```

10. **`Array.FindIndex()`**: Retorna o índice do primeiro elemento que atende a uma condição.
    ```csharp
    int[] numeros = { 1, 2, 3, 4 };
    int indice = Array.FindIndex(numeros, element => element > 2); // Retorna 2
    ```

11. **`Array.FindLast()`**: Retorna o último elemento que atende a uma condição.
    ```csharp
    int[] numeros = { 1, 2, 3, 4 };
    int encontrado = Array.FindLast(numeros, element => element > 2); // Retorna 4
    ```

12. **`Array.FindLastIndex()`**: Retorna o índice do último elemento que atende a uma condição.
    ```csharp
    int[] numeros = { 1, 2, 3, 4 };
    int indice = Array.FindLastIndex(numeros, element => element > 2); // Retorna 3
    ```

13. **`Array.TrueForAll()`**: Verifica se todos os elementos do array atendem a uma condição.
    ```csharp
    int[] numeros = { 1, 2, 3, 4 };
    bool todos = Array.TrueForAll(numeros, element => element > 0); // Retorna true
    ```

14. **`Array.BinarySearch()`**: Executa uma busca binária em um array ordenado.
    ```csharp
    int[] numeros = { 1, 2, 3, 4 };
    int indice = Array.BinarySearch(numeros, 3); // Retorna 2 (índice de "3" no array ordenado)
    ```

15. **`Array.ConstrainedCopy()`**: Semelhante ao `Array.Copy()`, mas lança uma exceção se a cópia não for bem-sucedida, garantindo maior segurança.
    ```csharp
    int[] origem = { 1, 2, 3, 4 };
    int[] destino = new int[4];
    Array.ConstrainedCopy(origem, 0, destino, 0, 4); // Copia elementos de "origem" para "destino"
    ```

### **Métodos de Extensão (LINQ)**

Além dos métodos padrão de `Array`, você também pode usar métodos de extensão fornecidos pelo [[LINQ]] (Language Integrated Query) para manipular arrays. Alguns exemplos incluem:

1. **`Select()`**: Projeta cada elemento de uma sequência em uma nova forma.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   var quadrados = numeros.Select(x => x * x); // Retorna [1, 4, 9, 16]
   ```

2. **`Where()`**: Filtra uma sequência de valores com base em um predicado.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   var maioresQueDois = numeros.Where(x => x > 2); // Retorna [3, 4]
   ```

3. **`OrderBy()` e `OrderByDescending()`**: Ordena os elementos de uma sequência.
   ```csharp
   int[] numeros = { 3, 1, 4, 2 };
   var ordenado = numeros.OrderBy(x => x); // Retorna [1, 2, 3, 4]
   ```

4. **`Sum()`**: Retorna a soma dos elementos.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int soma = numeros.Sum(); // Retorna 10
   ```

Esses métodos oferecem diversas maneiras de trabalhar com arrays, permitindo ordenações, filtragens, buscas e modificações dos dados de forma eficiente. O LINQ, em particular, facilita muito a manipulação de arrays em C# de maneira declarativa e intuitiva.

Sim, há ainda mais funcionalidades para manipulação de arrays em C# .NET! Além dos métodos padrão e dos métodos de extensão de LINQ que já mencionei, existem mais métodos e recursos avançados que podem ser úteis em cenários específicos. Vamos explorar mais alguns:

### **Outros Métodos de Array**

1. **`Clone()`**: Cria uma cópia superficial do array. Diferente de `Copy()`, ele retorna um objeto, que geralmente precisa ser convertido para o tipo de array original.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int[] copia = (int[])numeros.Clone(); // Cria uma cópia do array
   ```

2. **`GetValue(int index)` e `SetValue(object value, int index)`**: Obtém ou define o valor em um índice específico. Isso é útil principalmente para arrays multidimensionais.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int valor = (int)numeros.GetValue(2); // Retorna 3
   numeros.SetValue(10, 2); // Define o valor 10 na posição 2
   ```

3. **`CopyTo(Array array, int index)`**: Copia todos os elementos do array atual para outro array, começando em um índice especificado.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int[] copia = new int[4];
   numeros.CopyTo(copia, 0); // Copia todos os elementos de "numeros" para "copia"
   ```

4. **`Resize(ref T[] array, int newSize)`**: Redimensiona o array. Isso é útil para alterar o tamanho de arrays que já foram criados.
   ```csharp
   int[] numeros = { 1, 2, 3 };
   Array.Resize(ref numeros, 5); // Redimensiona para 5 elementos
   numeros[3] = 4;
   numeros[4] = 5; // Agora o array tem 5 elementos
   ```

5. **`Fill(T[] array, T value)`**: Preenche todo o array com um valor específico (disponível no .NET Core 2.1+).
   ```csharp
   int[] numeros = new int[5];
   Array.Fill(numeros, 42); // Preenche todos os elementos com 42
   ```

6. **`Empty<T>()`**: Retorna um array vazio do tipo especificado (disponível a partir do .NET Core 2.0).
   ```csharp
   int[] vazio = Array.Empty<int>(); // Cria um array vazio de inteiros
   ```

7. **`CreateInstance(Type elementType, int length)`**: Cria dinamicamente uma instância de um array de um tipo e tamanho especificados. Isso é útil quando o tipo é determinado em tempo de execução.
   ```csharp
   Array array = Array.CreateInstance(typeof(int), 5); // Cria um array de inteiros com 5 elementos
   ```

### **Métodos de LINQ para Arrays**

Alguns métodos de LINQ adicionais que podem ser utilizados com arrays:

1. **`Aggregate()`**: Executa uma operação acumulada (como uma soma ou multiplicação) em todos os elementos do array.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int produto = numeros.Aggregate((acc, x) => acc * x); // Retorna 24 (1 * 2 * 3 * 4)
   ```

2. **`Distinct()`**: Remove elementos duplicados de um array.
   ```csharp
   int[] numeros = { 1, 2, 2, 3, 4 };
   var unicos = numeros.Distinct(); // Retorna [1, 2, 3, 4]
   ```

3. **`GroupBy()`**: Agrupa os elementos de um array com base em uma chave.
   ```csharp
   int[] numeros = { 1, 2, 2, 3, 4 };
   var grupos = numeros.GroupBy(x => x); // Agrupa os números iguais
   ```

4. **`Any()`**: Verifica se algum elemento do array atende a uma condição.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   bool existe = numeros.Any(x => x > 3); // Retorna true
   ```

5. **`All()`**: Verifica se todos os elementos do array atendem a uma condição.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   bool todosMenoresQueCinco = numeros.All(x => x < 5); // Retorna true
   ```

6. **`Max()` e `Min()`**: Retorna o maior ou menor valor do array.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   int maior = numeros.Max(); // Retorna 4
   int menor = numeros.Min(); // Retorna 1
   ```

7. **`Average()`**: Retorna a média dos valores do array.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   double media = numeros.Average(); // Retorna 2.5
   ```

8. **`Take(int count)`** e **`Skip(int count)`**: Retorna um número específico de elementos do início ou ignora um número específico de elementos.
   ```csharp
   int[] numeros = { 1, 2, 3, 4 };
   var primeirosDois = numeros.Take(2); // Retorna [1, 2]
   var ignoraDois = numeros.Skip(2); // Retorna [3, 4]
   ```

9. **`Concat()`**: Concatena dois arrays.
   ```csharp
   int[] numeros1 = { 1, 2 };
   int[] numeros2 = { 3, 4 };
   var concatenados = numeros1.Concat(numeros2); // Retorna [1, 2, 3, 4]
   ```

10. **`ToArray()`**: Converte uma sequência LINQ em um array.
    ```csharp
    var numeros = Enumerable.Range(1, 5).ToArray(); // Gera um array [1, 2, 3, 4, 5]
    ```

### **Manipulação de Arrays Multidimensionais**

Para arrays multidimensionais ou *jagged arrays* (arrays de arrays), há algumas abordagens específicas:

1. **`GetLength(int dimension)`**: Retorna o comprimento de uma dimensão específica do array multidimensional.
   ```csharp
   int[,] matriz = new int[3, 4];
   int linhas = matriz.GetLength(0); // Retorna 3 (número de linhas)
   int colunas = matriz.GetLength(1); // Retorna 4 (número de colunas)
   ```

2. **`GetLowerBound(int dimension)` e `GetUpperBound(int dimension)`**: Retorna os limites inferior e superior de uma dimensão no array multidimensional.
   ```csharp
   int[,] matriz = new int[3, 4];
   int limiteInferior = matriz.GetLowerBound(0); // Retorna 0 (limite inferior da primeira dimensão)
   int limiteSuperior = matriz.GetUpperBound(0); // Retorna 2 (limite superior da primeira dimensão)
   ```

Esses métodos e propriedades avançados ampliam significativamente a capacidade de manipulação de arrays no C#. Dependendo das necessidades do seu projeto, esses recursos podem ser extremamente úteis para criar soluções eficientes.