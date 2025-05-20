---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
---
Um _stack trace_ é um registro da cadeia de chamadas de função que levou até o ponto atual de execução de um programa — geralmente gerado quando acontece um erro crítico, como um `panic`. Em [[Go]], esse rastreio é impresso automaticamente ao ocorrer um erro grave, mostrando o nome das funções, os arquivos e as linhas de código correspondentes. É uma ferramenta valiosa para depuração, permitindo entender “quem chamou quem” até o ponto da falha. Funciona como uma autópsia do programa: você vê exatamente quais decisões e caminhos de execução levaram à tragédia final. Quanto mais profundo o stack trace, maior a pilha de escolhas ruins.