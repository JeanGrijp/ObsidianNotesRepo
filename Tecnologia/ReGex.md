---
tags:
  - Tecnologia
---
## O que são expressões regulares Regex ?

Expressões regulares são uma sequência de caracteres que expressam um padrão de pesquisa. Geralmente abreviadas como Regex ou Regexp . Elas são especialmente usadas para encontrar ou substituir palavras em textos. Além disso, podemos testar se um texto está em conformidade com as regras que definimos.  
  
Por exemplo, digamos que você tenha uma lista de nomes de arquivos. E você só quer encontrar arquivos com a extensão pdf . Depois de digitar uma expressão, ^\w+\.pdf$ funcionará. O significado das definições nesta expressão ficará mais claro conforme as etapas progridem.

![](Pasted%20image%2020241005181713.png)

## Combinadores básicos

O caractere ou palavra que queremos encontrar é escrito diretamente. É semelhante a um processo de busca normal. Por exemplo, para encontrar a palavra `curious` no texto, digite o mesmo.

![](Pasted%20image%2020241005181915.png)

## Ponto `.` : Qualquer caractere

O período `.` permite selecionar qualquer caractere, incluindo caracteres especiais e espaços. Digite um período `.` no campo Regex para prosseguir.

![](Pasted%20image%2020241005182008.png)

## Conjuntos de caracteres `[abc]`

Se um dos caracteres em uma palavra pode ser vários caracteres, escrevemos entre colchetes `[]` com todos os caracteres alternativos. Por exemplo, para escrever uma expressão que pode encontrar todas as palavras no texto, digite os caracteres `a` , `e` , `i` , `o` , `u` adjacentemente dentro de colchetes **[]** .

![](Pasted%20image%2020241005182215.png)

## Conjuntos de caracteres negados [^abc]

Para encontrar todas as palavras no texto abaixo, exceto `ber` e `bor` , digite e e o lado a lado após o caractere circunflexo `^` dentro de colchetes `[]` .

![](Pasted%20image%2020241005182739.png)

## Intervalo de letras `[az]`

Para encontrar as letras no intervalo especificado, a letra inicial e a letra final são escritas entre colchetes `[]` com um traço entre elas `-`. É sensível a maiúsculas e minúsculas. Digite a expressão que selecionará todas as letras minúsculas entre `e` e `o`, incluindo elas mesmas.

![](Pasted%20image%2020241005183134.png)

## Intervalo numérico `[0-9]`

Para encontrar os números no intervalo especificado, o número inicial e o número final são escritos entre colchetes `[]` com um traço `-` entre eles. Escreva uma expressão que selecionará todos os números entre `3` e `6`, incluindo eles mesmos.

![](Pasted%20image%2020241005183412.png)

