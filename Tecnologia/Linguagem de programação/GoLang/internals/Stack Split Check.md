---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
---
No [[Go]], o _stack split check_ é uma verificação automática inserida no início de cada função para garantir que a [[Tecnologia/Linguagem de programação/GoLang/internals/Stack]] da [goroutine](Goroutine.md) tem espaço suficiente para acomodar a execução dessa função. Como as goroutines começam com uma stack muito pequena (cerca de 2 KB), essa checagem previne estouros silenciosos ao avaliar se há memória suficiente disponível. Se não houver espaço, o runtime aciona um mecanismo para realocar a stack, movendo todo o conteúdo atual para um novo espaço maior na memória e ajustando os ponteiros como se nada tivesse acontecido. Esse processo é invisível para o programador, mas crucial para que o modelo leve de concorrência do Go funcione sem quebrar tudo.