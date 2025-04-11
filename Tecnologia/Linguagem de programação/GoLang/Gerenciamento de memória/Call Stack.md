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

No Go, cada função que você chama também entra na stack, como em qualquer [[linguagem procedural]] que não acordou ontem. A diferença está na forma como o Go gerencia essa stack por trás das cortinas. Em vez de criar uma stack grandona e fixa por [[thread]], como acontece em linguagens como C ou Java, o Go tem um modelo mais... econômico. Cada [[goroutines]] (que são basicamente threads leves gerenciadas pelo próprio runtime do Go) começa com uma stack minúscula, normalmente em torno de 2 KB, isso é ridiculamente pequeno.

Essa stack pequenininha é **dinâmica**. O que significa isso? Significa que, à medida que a função chama outra função, e assim por diante, o Go percebe quando está ficando apertado e realoca a stack para um espaço maior. Sim, ele literalmente copia a pilha inteira para outro lugar na memória. Parece ineficiente, mas na prática é bem rápido e muito mais econômico do que reservar 1 MB por goroutine só porque você tem sonhos grandes demais. É esse esquema que permite ao Go criar centenas de milhares de goroutines sem mandar sua máquina pro hospital.

Mas nem tudo são flores, como o stack é móvel, o Go precisa tomar muito cuidado com [[ponteiros]]. Diferente de C, onde um ponteiro pode apontar pro nada e te causar pesadelos, no Go o runtime cuida de atualizar tudo quando o stack é movido.

Além disso, quando ocorre um erro, como um `panic`, o Go consegue imprimir um [[stack trace]] que mostra exatamente a cadeia de chamadas que levou ao desastre. Esse stack trace é capturado a partir das informações armazenadas na stack das [[goroutines]]. Não é super detalhado como um debugger de verdade, mas serve pra você olhar com vergonha e dizer: “é, fui eu mesmo que escrevi essa aberração.”

Então, resumindo em parágrafo como você gosta: o call stack no Go é gerenciado de forma automática e dinâmica, permitindo leveza e escalabilidade com goroutines, mas isso exige um runtime esperto que consiga crescer, mover e atualizar o stack conforme o programa executa. Isso traz benefícios imensos, mas também uma certa mágica preta sob o capô, que faz debugging e análise de performance serem um pouco mais desafiadores, especialmente se você insistir em não ler o código-fonte do runtime porque “dá sono”.

### Como o Go sabe que precisa crescer o stack?

Isso é onde o Go fica metido a esperto. Toda vez que uma função é chamada, o runtime precisa garantir que haja espaço suficiente no stack da goroutine para que a função execute. Isso inclui espaço para variáveis locais, argumentos e o que mais for necessário dentro daquele frame. Só que, lembra? O stack começa ridiculamente pequeno. Pra resolver isso, o compilador do Go insere automaticamente um código de verificação no início de cada função. Isso é conhecido como o famoso **[[stack split check]]**. Traduzindo do Goês para o Idioma dos Mortais: é uma checagem que pergunta "tenho espaço suficiente pra rodar essa função aqui ou não?"

Se a resposta for “sim”, maravilha. A função executa normalmente e ninguém sofre. Se a resposta for “não”, o Go chama uma função especial, `newstack()`, que realiza o crescimento da pilha.

Agora vem a parte legal (ou trágica, depende de quanto você gosta de ver seu programa fazendo cópia de memória no meio da execução): essa função `newstack()` cria uma nova área de memória maior, **copia todo o conteúdo da stack atual pra lá**, ajusta os ponteiros, e depois continua a execução como se nada tivesse acontecido. É como trocar o chão enquanto você ainda está andando — e torcer pra ninguém perceber.

Isso funciona porque o Go controla todo o ambiente da goroutine. Nenhum ponteiro externo aponta diretamente pro stack (pelo menos, não deveria — o compilador e o [[garbage collector]] trabalham pra evitar isso), então é seguro mover tudo sem causar um incêndio na memória. Por isso, o Go consegue crescer stacks conforme a necessidade sem causar overhead constante. Você paga o preço da realocação só quando realmente precisa. Isso também explica por que goroutines são tão leves: elas só consomem o que for necessário, e vão crescendo conforme o uso real, em vez de começar como um trambolho de 1 MB cada uma.

