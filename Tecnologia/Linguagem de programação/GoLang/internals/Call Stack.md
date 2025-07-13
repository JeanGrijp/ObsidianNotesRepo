---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
  - Security
---
A _stack_, ou pilha de execução, é uma estrutura de dados usada internamente pelas linguagens de programação para controlar a ordem de execução das funções. Ela segue uma lógica chamada **[[LIFO]]**, que significa "Last In, First Out" — ou seja, a última função que entrou na pilha é a primeira que vai sair. É como empilhar livros: o último que você colocou em cima é o primeiro que você vai tirar. Essa pilha não é um conceito visual bonitinho só pra programador iniciante se sentir incluído; ela é real, ela mora na memória do processo e serve pra guardar o que está acontecendo a cada momento da execução.

Quando uma função é chamada, o programa empilha um **frame**, que é como um pacote com informações: quais são os argumentos da função, quais variáveis ela declarou internamente, e — talvez o mais importante — qual o endereço de memória que ele precisa voltar depois que essa função terminar. Assim que a função conclui seu trabalho, esse frame é retirado da [Stack](Tecnologia/Linguagem%20de%20programação/GoLang/Estrutura%20de%20dados/Stack.md), e o controle volta pra função anterior, que estava de braços cruzados esperando isso acontecer.

Esse processo de empilhar e desempilhar acontece de forma automática, você não precisa fazer nada. A não ser que você seja um desenvolvedor em [[Go]] querendo entender por que o seu programa explodiu com um `stack overflow`, o que geralmente significa que uma função chamou a si mesma tantas vezes que a pilha não teve mais espaço pra guardar os frames.

No Go, cada função que você chama também entra na stack, como em qualquer [[linguagem procedural]] que não acordou ontem. A diferença está na forma como o Go gerencia essa stack por trás das cortinas. Em vez de criar uma stack grandona e fixa por [[Thread]], como acontece em linguagens como C ou Java, o Go tem um modelo mais... econômico. Cada [[Goroutine]] (que são basicamente threads leves gerenciadas pelo próprio runtime do Go) começa com uma stack minúscula, normalmente em torno de 2 KB, isso é ridiculamente pequeno.

Essa stack pequenininha é **dinâmica**. O que significa isso? Significa que, à medida que a função chama outra função, e assim por diante, o Go percebe quando está ficando apertado e realoca a stack para um espaço maior. Sim, ele literalmente copia a pilha inteira para outro lugar na memória. Parece ineficiente, mas na prática é bem rápido e muito mais econômico do que reservar 1 MB por goroutine só porque você tem sonhos grandes demais. É esse esquema que permite ao Go criar centenas de milhares de goroutines sem mandar sua máquina pro hospital.

Mas nem tudo são flores, como o stack é móvel, o Go precisa tomar muito cuidado com [[ponteiros]]. Diferente de C, onde um ponteiro pode apontar pro nada e te causar pesadelos, no Go o runtime cuida de atualizar tudo quando o stack é movido.

Além disso, quando ocorre um erro, como um `panic`, o Go consegue imprimir um [[Stack Trace]] que mostra exatamente a cadeia de chamadas que levou ao desastre. Esse stack trace é capturado a partir das informações armazenadas na stack das [[Goroutine]]. Não é super detalhado como um debugger de verdade, mas serve pra você olhar com vergonha e dizer: “é, fui eu mesmo que escrevi essa aberração.”

Então, resumindo em parágrafo como você gosta: o call stack no Go é gerenciado de forma automática e dinâmica, permitindo leveza e escalabilidade com goroutines, mas isso exige um runtime esperto que consiga crescer, mover e atualizar o stack conforme o programa executa. Isso traz benefícios imensos, mas também uma certa mágica preta sob o capô, que faz debugging e análise de performance serem um pouco mais desafiadores, especialmente se você insistir em não ler o código-fonte do runtime porque “dá sono”.

### Como o Go sabe que precisa crescer o stack?

Isso é onde o Go fica metido a esperto. Toda vez que uma função é chamada, o runtime precisa garantir que haja espaço suficiente no stack da goroutine para que a função execute. Isso inclui espaço para variáveis locais, argumentos e o que mais for necessário dentro daquele frame. Só que, lembra? O stack começa ridiculamente pequeno. Pra resolver isso, o compilador do Go insere automaticamente um código de verificação no início de cada função. Isso é conhecido como o famoso **[[Stack Split Check]]**. Traduzindo do Goês para o Idioma dos Mortais: é uma checagem que pergunta "tenho espaço suficiente pra rodar essa função aqui ou não?"

Se a resposta for “sim”, maravilha. A função executa normalmente e ninguém sofre. Se a resposta for “não”, o Go chama uma função especial, `newstack()`, que realiza o crescimento da pilha.

Agora vem a parte legal (ou trágica, depende de quanto você gosta de ver seu programa fazendo cópia de memória no meio da execução): essa função `newstack()` cria uma nova área de memória maior, **copia todo o conteúdo da stack atual pra lá**, ajusta os ponteiros, e depois continua a execução como se nada tivesse acontecido. É como trocar o chão enquanto você ainda está andando — e torcer pra ninguém perceber.

Isso funciona porque o Go controla todo o ambiente da goroutine. Nenhum ponteiro externo aponta diretamente pro stack (pelo menos, não deveria — o compilador e o [[Garbage Collector]] trabalham pra evitar isso), então é seguro mover tudo sem causar um incêndio na memória. Por isso, o Go consegue crescer stacks conforme a necessidade sem causar overhead constante. Você paga o preço da realocação só quando realmente precisa. Isso também explica por que goroutines são tão leves: elas só consomem o que for necessário, e vão crescendo conforme o uso real, em vez de começar como um trambolho de 1 MB cada uma.

### Heap x Stack

No Go, o gerenciamento de memória é dividido entre duas regiões principais: a **stack** e o **[[Heap]]**. A stack é usada para armazenar variáveis locais de curta duração, parâmetros de função e tudo que pode ser descartado rapidamente assim que a função termina. Cada vez que uma função é chamada, o Go aloca um pequeno espaço na stack — chamado de _stack frame_ — onde ele guarda todas as informações relacionadas àquela execução. Esse espaço inclui os argumentos passados, variáveis locais e um ponteiro que indica onde a execução deve continuar quando a função retornar. Quando a função termina, esse frame é simplesmente removido da stack, liberando o espaço de forma rápida e eficiente.

A stack no Go é única para cada **goroutine**. Isso significa que toda goroutine que você cria tem sua própria pilha, independente das outras. Essas stacks são minúsculas por padrão (começam com apenas 2KB), mas têm a habilidade mágica de crescer dinamicamente se for necessário, através de um processo chamado **stack splitting**. Isso permite que milhares ou até milhões de goroutines coexistam sem fritar sua máquina, o que é uma façanha que linguagens mais ortodoxas como Java só conseguem com oração.

Já o heap é onde moram as variáveis que o compilador acha que vão viver por mais tempo, ou que precisam ser acessadas fora do escopo da função onde nasceram. O Go decide o destino de cada variável usando uma técnica chamada **escape analysis**, que basicamente responde à pergunta: “essa variável vai vazar do contexto onde foi criada?” Se sim, ela vai direto pro heap. Isso acontece, por exemplo, quando você retorna o ponteiro de uma variável local, ou quando uma função armazena referências em uma estrutura que vai sobreviver à execução atual.

As funções em si — ou seja, o código delas — não vão pra heap nem pra stack. Elas são armazenadas numa região separada da memória chamada **text segment**, que é onde vive o binário compilado. Quando você chama uma função, o Go basicamente empilha um novo frame na stack da goroutine ativa e pula praquele endereço no text segment. Quando ela termina, ele volta pro ponto anterior e a vida segue.

Esse modelo é extremamente eficiente. Usar a stack como área principal de alocação temporária reduz a carga do **garbage collector**, que só precisa se preocupar com as variáveis no heap. Mas como nem tudo é perfeito, você ainda precisa estar ciente de quando suas variáveis vão parar lá, pra evitar alocações desnecessárias e gargalos invisíveis de desempenho. E sim, isso significa que _às vezes_ vale a pena olhar os relatórios de escape do compilador — por mais deprimentes que eles sejam.

##### As funções em Go

Já as funções, diferentemente das variáveis, têm um destino completamente diferente. O código da função em si é armazenado em uma região especial da memória chamada text segment, também conhecida como code segment. Essa parte da memória é onde vive o código executável do programa. Quando você chama uma função, a CPU salta para o endereço correspondente no text segment, e as variáveis que a função usa são empilhadas na stack ou alocadas no heap, dependendo do que o compilador decidir.

Essa arquitetura torna o Go extremamente eficiente para lidar com concorrência e múltiplas goroutines. Cada goroutine tem sua própria stack que começa pequena e pode crescer de forma dinâmica, permitindo que você tenha milhares de tarefas leves rodando ao mesmo tempo. Ao mesmo tempo, a separação clara entre stack, heap e text segment ajuda o compilador a otimizar a execução e o uso de memória de forma bastante eficaz.


###### Alocação na Stack

```go
package main

import "fmt"

func main() {
	x := 42
	printValue(x)
}

func printValue(val int) {
	fmt.Println(val)
}
```

Nesse exemplo, a variável `x` é um valor simples. Ele é passado por **valor** para a função `printValue`, e não é retornado, nem usado fora do `main`. Como nada escapa, o compilador é feliz e aloca tudo isso na **stack**.

###### Alocação na Heap

```go
package main

import "fmt"

func main() {
	ptr := createPointer()
	fmt.Println(*ptr)
}

func createPointer() *int {
	x := 42
	return &x
}
```

Agora temos o mesmo valor, `42`, e o mesmo tipo de variável. Mas olha o crime aqui: estamos **retornando um ponteiro para uma variável local**. A stack de `createPointer` vai sumir assim que a função retornar, então se `x` estivesse ali, estaríamos acessando memória morta. O compilador detecta isso e joga `x` direto no **heap**, garantindo que ele sobreviva após o retorno.

### Moral da história

O destino da variável não depende do que ela é, mas do **que você faz com ela**. Se ela ficar no escopo, stack. Se ela sair do escopo, heap. Se você passar por valor, stack. Se você passar por ponteiro, ou armazenar a referência em outro lugar, o compilador provavelmente vai colocá-la no heap.

Isso é ótimo porque o Go faz tudo isso pra você, mas também significa que, se você não souber o que está fazendo, pode estar enchendo o heap de lixo emocional que devia ter ficado na stack.
