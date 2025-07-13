---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
---
Uma _thread_ é uma unidade de execução dentro de um processo. É o que permite que um programa realize múltiplas tarefas aparentemente ao mesmo tempo. Em sistemas tradicionais, cada thread possui sua própria [[stack]] e é gerenciada pelo sistema operacional, com alto custo de criação e comutação. Em contrapartida, o [[Go]] abstrai esse conceito com as [[Goroutine]], que são unidades de execução mais leves que threads, gerenciadas pelo runtime do Go, muito mais leves e eficientes para tarefas concorrentes. Mesmo assim, entender o conceito de thread continua sendo útil, porque é a base sobre a qual a concorrência moderna foi construída.

Cada thread, no contexto de programação, tem a sua **própria [[Call Stack]]**. Isso significa que quando você cria uma nova thread, o sistema operacional ou o runtime da linguagem aloca uma nova região de memória exclusiva pra ela, onde vai empilhar as chamadas de função feitas dentro dessa thread. Essa pilha é isolada das demais threads: se duas threads estiverem rodando ao mesmo tempo e ambas chamarem uma função chamada `processarDados()`, cada uma delas vai ter a _sua própria versão empilhada_ de `processarDados()` na sua respectiva stack. Elas não brigam pela mesma memória porque estão cada uma vivendo seu mundinho solitário de execução.

A stack da thread é onde ficam armazenados os dados temporários da execução: parâmetros de função, variáveis locais e o endereço de retorno — ou seja, pra onde voltar quando a função acabar. Quando uma função é chamada, o frame é empilhado na stack da thread atual. Quando ela retorna, o frame é removido. É uma dança organizada de entrada e saída de contextos de execução, sempre seguindo o modelo [[LIFO]]: a última chamada é a primeira a sair.

Agora, por que isso importa? Porque se você fizer besteira dentro de uma thread — tipo criar recursão infinita ou entupir a execução com chamadas profundas — _só a stack daquela thread vai explodir_. As outras threads, felizes e inconscientes, continuam rodando como se nada tivesse acontecido.

E em Go? A coisa muda um pouco, porque Go não usa threads diretamente. Ele usa goroutines, que são programaticamente muito mais leves, mas o princípio é parecido: **cada goroutine tem sua própria stack separada**, e ela funciona da mesma forma que a stack de uma thread — só que com o charme extra de ser pequena, flexível e controlada pelo runtime em vez do sistema operacional.

### Um pouco mais sobre stacks dinâmicas das goroutines

Uma das características mais importantes das goroutines é o uso de **stacks dinâmicas**, que são controladas inteiramente pelo runtime do Go. Diferente das threads tradicionais do sistema operacional que geralmente alocam uma stack fixa de 1 MB ou mais, cada goroutine começa com uma stack extremamente pequena, geralmente **apenas 2 KB**. Essa escolha reduz drasticamente o custo de criação de novas goroutines e é um dos principais motivos pelos quais é possível criar **milhares ou até milhões de goroutines** em uma única aplicação Go sem comprometer a memória do sistema. A medida que a execução da goroutine avança, se o espaço da stack se tornar insuficiente (por exemplo, devido a chamadas de função aninhadas ou uso intensivo de variáveis locais), o runtime detecta essa situação e **realoca a stack em uma região maior de memória**, copiando seu conteúdo atual e ajustando os ponteiros automaticamente. Esse processo é **transparente para o desenvolvedor** e não exige nenhuma intervenção manual. Além disso, o runtime é capaz de **reduzir o tamanho da stack** de uma goroutine quando percebe que ela não precisa mais de tanta memória. Essa **flexibilidade bidirecional** permite uma gestão de memória muito mais eficiente do que stacks fixas, e ajuda a manter a escalabilidade e responsividade da aplicação.

Vale lembrar que essa operação de crescimento (ou encolhimento) da stack pode ter custo de desempenho pontual, especialmente se ocorrer com frequência. Por isso, em cenários críticos de performance, é possível otimizar o uso da stack escrevendo funções com profundidade de chamada controlada e evitando estruturas muito grandes em variáveis locais.

### **Como o Go lida com preempção**, ou seja, como ele lida com aquelas goroutines folgadas que decidem nunca mais sair da CPU?

O [[scheduler]] do Go precisa garantir que nenhuma goroutine fique se achando a dona da CPU. Pra isso, existe o conceito de **preempção**, que basicamente é o runtime dizendo “chega, tua vez acabou, deixa o coleguinha brincar”. Só que o Go não faz isso de forma brutal, como alguns sistemas operacionais que simplesmente pausam a thread no meio de qualquer instrução. Em vez disso, o Go usa o modelo de **preempção cooperativa**, o que significa que ele só pode interromper a execução de uma goroutine **em pontos seguros** do código, lugares estrategicamente definidos onde o runtime sabe que pode pausar sem quebrar tudo.

Esses pontos seguros (ou _safe points_, se você quiser parecer mais chique) são colocados pelo compilador em lugares como o início de loops, chamadas de função ou outras instruções que o runtime monitora. A ideia é evitar que uma goroutine fique rodando pra sempre sem dar chance pras outras, tipo aquele colega de reunião que não larga o microfone.

Antes do Go 1.14, isso era um problema sério. Se uma goroutine entrasse num loop longo sem fazer nenhuma chamada externa, ela podia simplesmente **monopolizar um núcleo da CPU** e impedir que outras goroutines rodassem. Era como ter um funcionário que esqueceu de sair pra almoço e não deixa ninguém mais usar a sala de reunião. A partir do Go 1.14, o runtime ganhou um superpoder: o compilador passou a inserir instruções especiais nos loops e outros trechos críticos, permitindo que o agendador dê aquele **“tapinha no ombro” da goroutine** e diga: "tempo esgotado, agora deixa o próximo". Quando isso acontece, o estado da execução é salvo e outra goroutine assume a execução no mesmo `P`. Esse esquema garante que nenhuma goroutine abuse do tempo de CPU e ajuda o Go a manter o show rodando suave — sem threads explodindo, sem pausas indesejadas, e sem você precisar escrever uma linha de código pra gerenciar isso.

### Limitações e armadilhas do Go Scheduler (sim, ele também tem seus dias ruins)

O Go scheduler é ótimo — ele faz muito com pouco: organiza milhares de goroutines com algumas threads reais, empilha, desempilha, redistribui... tudo isso quase sem você notar. Mas ele **não é mágico nem infalível**. Em algumas situações específicas, ele pode se tornar um **gargalo**, especialmente quando a arquitetura da aplicação força os limites do modelo `G:M:P`.

Um dos principais pontos de atenção está no valor de `GOMAXPROCS`. Como esse valor define quantos `P`s existem, ele **limita o número de goroutines que podem executar código Go ao mesmo tempo**. Se `GOMAXPROCS=2`, por exemplo, mesmo que você tenha 10 mil goroutines prontas pra rodar, só duas vão de fato executar simultaneamente. As outras ficam esperando na fila. Isso é ótimo pra controle de paralelismo, mas pode gerar filas grandes e latência inesperada se o número de goroutines prontas crescer rápido demais.

Outro fator crítico: o scheduler não tem conhecimento semântico do que suas goroutines estão fazendo. Ele não sabe se a goroutine está processando uma requisição urgente ou só atualizando um cache irrelevante. O escalonamento é feito com base em prontidão, não em prioridade. Isso significa que goroutines com workloads diferentes podem **competir igualmente por tempo de execução**, o que nem sempre é ideal. Em alguns casos, goroutines importantes podem ser preemptadas por outras menos críticas, impactando a qualidade do serviço.

O scheduler também pode sofrer se você tiver **muitas goroutines prontas ao mesmo tempo**, mas com uma lógica que exige muita alternância entre elas (ex: milhares de canais se comunicando em alta frequência). Isso pode causar overhead de troca de contexto e pressionar a fila global de goroutines — que, diferente das filas locais dos `P`s, exige lock e pode se tornar um **ponto de contenção**.

Além disso, é comum ver devs criando goroutines demais sem pensar no lifecycle delas. Se você esquece de controlar o tempo de vida de uma goroutine (como não cancelá-la via context, por exemplo), elas continuam vivas, mesmo que não estejam fazendo nada útil. Isso gera **acúmulo silencioso**: pilhas crescendo, memória sendo usada à toa, e mais carga sobre o agendador.

Por fim, como o Go não tem preempção baseada em tempo real (como certos kernels de SO), uma goroutine que faz **uso intensivo de CPU sem pontos de preempção** ainda pode gerar starvation (as outras goroutines ficam sem tempo de execução). Isso é raro com as melhorias do Go 1.14+, mas ainda possível se você escrever código que "engole" o processador por muito tempo sem pausas.


### chegou até aqui?

Entender como o Go lida com agendamento, preempção, chamadas bloqueantes e stacks minúsculas não só te ajuda a escrever código mais eficiente, como também evita que você saia criando goroutine igual panfleto em semáforo e depois não saiba de onde vem o vazamento de memória. Se esse artigo te ajudou, manda pra aquele(a) amigo(a) que acha que `go func() {}` é solução mágica pra tudo. E se você encontrou algum erro, manda também, estou aprendendo sobre o tema, e além disso porque o scheduler pode escalar milhares de goroutines, mas eu ainda não escalo múltiplas versões de mim mesmo.

Valeu demais por acompanhar. Até a próxima execução paralela. #Beba_agua.