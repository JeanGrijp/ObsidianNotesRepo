---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
---
Em Go, a heap é uma área de memória utilizada para armazenar dados que precisam sobreviver ao escopo onde foram criados. Essa região é usada quando a linguagem detecta que o valor de uma variável pode ser necessário após o fim da função onde ela foi declarada. Por isso, a heap oferece maior flexibilidade e é essencial para manter dados vivos enquanto houver referências a eles em uso. Um exemplo simples dessa alocação acontece quando utilizamos a função `new`, que solicita memória e retorna um ponteiro para esse espaço, permitindo o acesso ao dado por fora do escopo local.

```go
func allocate() *int {
	x := new(int)
	*x = 42
	return x
}
```

Nesse trecho, `x` é alocado na heap porque a função retorna um ponteiro para ele. O compilador entende que, como esse valor será acessado depois, ele não pode ficar na stack — que seria descartada assim que a função terminasse.

Além da função `new`, Go oferece a função `make`, que também aloca memória na heap, especialmente para tipos compostos como slices, maps e channels. Essas estruturas muitas vezes precisam ser compartilhadas entre funções e goroutines, exigindo uma alocação mais duradoura. O uso de slices com `make` é um exemplo clássico de como a heap permite estruturas mais complexas e flexíveis no programa.

```go
func main() {
	slice := make([]int, 5)
	slice[0] = 10
	fmt.Println(slice)
}
```

Aqui, o slice é criado com `make`, o que indica que o array subjacente muito provavelmente foi alocado na heap. Isso é necessário para garantir que ele possa ser manipulado e acessado mesmo após a saída do escopo da função que o criou, caso haja alguma referência circulando.

O tempo de vida dos dados na heap é gerenciado automaticamente pelo Garbage Collector (GC) do Go. Ele monitora objetos alocados na heap e libera aqueles que não estão mais sendo utilizados pelo programa. Isso significa que, ao contrário da stack, a heap não é liberada automaticamente com o fim de uma função, mas sim quando o runtime determina que não há mais referências para aquele dado. É isso que permite que variáveis vivam enquanto forem necessárias e desapareçam quando não forem mais úteis.

```go
func main() {
	slice := make([]int, 1000000)
	// Após o fim da função, slice pode ser limpo pelo GC
}
```

Neste exemplo, um slice de um milhão de elementos é criado e armazenado na heap. Enquanto a variável `slice` existir e estiver acessível, o GC não irá liberá-la. Mas assim que ela sair do escopo e não houver mais referência ativa, o coletor de lixo poderá recuperar a memória.

A heap também se mostra essencial quando estruturas são passadas por referência entre partes diferentes do programa. Imagine uma função que cria um grande slice, preenche seus dados e retorna um ponteiro para que outra função possa utilizá-lo. O Go identifica esse padrão e garante que os dados estejam na heap, mantendo a integridade da memória mesmo após o fim da função criadora.

```go
func createLargeSlice() *[]int {
	data := make([]int, 1000000)
	for i := 0; i < len(data); i++ {
		data[i] = i
	}
	return &data
}

func main() {
	slice := createLargeSlice()
	fmt.Println((*slice)[999999])
}
```

Nesse exemplo, a função `createLargeSlice` retorna um ponteiro para um slice que foi alocado com `make`. O compilador coloca esse slice na heap porque ele será acessado fora da função onde foi criado. Enquanto o ponteiro estiver sendo usado no `main`, o GC mantém os dados intactos. Quando a última referência ao slice for perdida, o espaço será automaticamente liberado.

Por fim, apesar da flexibilidade, a heap tem suas limitações. Ela é mais lenta que a stack, tanto na alocação quanto na liberação, e pode causar fragmentação de memória se for mal utilizada. Como ela depende do Garbage Collector, o tempo de execução de um programa pode sofrer pausas inesperadas para que a coleta aconteça. Ainda assim, a heap é indispensável para dados de longa duração e para estruturas que precisam ser compartilhadas.


