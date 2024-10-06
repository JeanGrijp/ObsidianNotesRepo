---
tags:
  - Tecnologia
  - Dotnet
---
No C# .NET, existem diversos métodos para manipulação de strings. Aqui estão alguns dos mais utilizados:

1. **`Length`**: Retorna o número de caracteres na string.
   ```csharp
   string texto = "Hello";
   int tamanho = texto.Length; // Retorna 5
   ```

2. **`Substring(int startIndex, int length)`**: Retorna uma sub-string começando no índice especificado, com o tamanho definido.
   ```csharp
   string texto = "Hello World";
   string subTexto = texto.Substring(0, 5); // Retorna "Hello"
   ```

3. **`Replace(string oldValue, string newValue)`**: Substitui todas as ocorrências de uma sub-string por outra.
   ```csharp
   string texto = "Hello World";
   string novoTexto = texto.Replace("World", "C#"); // Retorna "Hello C#"
   ```

4. **`ToUpper()` e `ToLower()`**: Converte todos os caracteres da string para maiúsculas ou minúsculas.
   ```csharp
   string texto = "Hello";
   string maiuscula = texto.ToUpper(); // Retorna "HELLO"
   string minuscula = texto.ToLower(); // Retorna "hello"
   ```

5. **`Trim()` / `TrimStart()` / `TrimEnd()`**: Remove espaços em branco (ou caracteres específicos) do início e do fim da string.
   ```csharp
   string texto = "   Hello   ";
   string textoTrimmed = texto.Trim(); // Retorna "Hello"
   ```

6. **`Split(char separator)`**: Divide a string em um array de sub-strings com base em um caractere separador.
   ```csharp
   string texto = "apple,banana,cherry";
   string[] frutas = texto.Split(','); // Retorna ["apple", "banana", "cherry"]
   ```

7. **`Join(string separator, string[] values)`**: Junta um array de strings em uma única string, utilizando o separador especificado.
   ```csharp
   string[] frutas = { "apple", "banana", "cherry" };
   string texto = string.Join(", ", frutas); // Retorna "apple, banana, cherry"
   ```

8. **`Contains(string value)`**: Verifica se uma string contém uma determinada substring.
   ```csharp
   string texto = "Hello World";
   bool contem = texto.Contains("World"); // Retorna true
   ```

9. **`StartsWith(string value)` e `EndsWith(string value)`**: Verificam se uma string começa ou termina com a substring especificada.
   ```csharp
   string texto = "Hello World";
   bool comecaCom = texto.StartsWith("Hello"); // Retorna true
   bool terminaCom = texto.EndsWith("World"); // Retorna true
   ```

10. **`IndexOf(string value)`** e **`LastIndexOf(string value)`**: Retornam o índice da primeira ou última ocorrência de uma substring.
    ```csharp
    string texto = "Hello World";
    int index = texto.IndexOf("World"); // Retorna 6
    ```

11. **`Insert(int startIndex, string value)`**: Insere uma string em uma posição específica dentro de outra string.
    ```csharp
    string texto = "Hello World";
    string novoTexto = texto.Insert(5, ","); // Retorna "Hello, World"
    ```

12. **`Remove(int startIndex, int count)`**: Remove um número de caracteres a partir de um índice específico.
    ```csharp
    string texto = "Hello World";
    string novoTexto = texto.Remove(5, 6); // Retorna "Hello"
    ```

13. **`PadLeft(int totalWidth)` e `PadRight(int totalWidth)`**: Preenche a string com espaços ou caracteres até que ela tenha o comprimento especificado.
    ```csharp
    string texto = "Hello";
    string textoPadLeft = texto.PadLeft(10);  // Retorna "     Hello"
    string textoPadRight = texto.PadRight(10); // Retorna "Hello     "
    ```

14. **`Format(string format, params object[] args)`**: Substitui marcadores em uma string por valores específicos, como o método `String.Format`.
    ```csharp
    string nome = "Jean";
    int idade = 30;
    string texto = string.Format("Meu nome é {0} e tenho {1} anos", nome, idade); 
    // Retorna "Meu nome é Jean e tenho 30 anos"
    ```

15. **`ToCharArray()`**: Converte a string em um array de caracteres.
    ```csharp
    string texto = "Hello";
    char[] caracteres = texto.ToCharArray(); // Retorna ['H', 'e', 'l', 'l', 'o']
    ```

16. **`Compare(string strA, string strB)`**: Compara duas strings e retorna um valor inteiro que indica a relação entre elas (-1, 0, 1).
    ```csharp
    string texto1 = "Hello";
    string texto2 = "World";
    int resultado = string.Compare(texto1, texto2); // Retorna -1 (porque "Hello" é menor que "World")
    ```

17. **`Equals(string value)`**: Compara se duas strings são iguais (ou não).
    ```csharp
    string texto1 = "Hello";
    string texto2 = "hello";
    bool iguais = texto1.Equals(texto2, StringComparison.OrdinalIgnoreCase); // Retorna true (comparação ignorando maiúsculas/minúsculas)
    ```

18. **`Normalize()`**: Normaliza a string para um formulário Unicode específico (muito útil para internacionalização).
    ```csharp
    string texto = "café";
    string normalizado = texto.Normalize(); // Retorna a string em uma forma Unicode normalizada
    ```

19. **`IsNullOrEmpty(string value)` e `IsNullOrWhiteSpace(string value)`**: Verifica se uma string é nula ou vazia (ou contém apenas espaços em branco no caso de `IsNullOrWhiteSpace`).
    ```csharp
    string texto = "";
    bool vazioOuNulo = string.IsNullOrEmpty(texto); // Retorna true
    ```

20. **`Concat(params string[] values)`**: Junta várias strings em uma única string.
    ```csharp
    string texto1 = "Hello";
    string texto2 = "World";
    string resultado = string.Concat(texto1, " ", texto2); // Retorna "Hello World"
    ```

21. **`Intern(string value)`**: Controla o uso de memória de strings duplicadas. Retorna a versão "internada" de uma string para reduzir o consumo de memória.
    ```csharp
    string texto = "Hello";
    string internado = string.Intern(texto); // Interna a string para otimização de memória
    ```

22. **`Copy(string str)`**: Cria uma cópia da string, embora a partir do C# 8.0, o uso de `Copy` tenha sido desencorajado em favor de outros métodos.
    ```csharp
    string texto = "Hello";
    string copia = string.Copy(texto); // Retorna uma cópia de "Hello"
    ```

23. **`Trim(char[] chars)`**: Remove caracteres específicos do início e do fim de uma string (não apenas espaços em branco).
    ```csharp
    string texto = "--Hello--";
    string textoTrimmed = texto.Trim('-'); // Retorna "Hello"
    ```

24. **`StartsWith(char value)` e `EndsWith(char value)`**: Podem também aceitar caracteres, não apenas strings.
    ```csharp
    string texto = "Hello";
    bool comecaComH = texto.StartsWith('H'); // Retorna true
    ```

Esses métodos ampliam as opções para manipular strings no C# e ajudam a realizar uma grande variedade de operações, desde comparações e formatações até remoções e inserções de caracteres.