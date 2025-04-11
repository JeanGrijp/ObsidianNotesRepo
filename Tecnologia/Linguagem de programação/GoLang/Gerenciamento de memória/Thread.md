---
tags:
  - Tecnologia
  - GoLang
  - DataStructures
---
Uma _thread_ é uma unidade de execução dentro de um processo. É o que permite que um programa realize múltiplas tarefas aparentemente ao mesmo tempo. Em sistemas tradicionais, cada thread possui sua própria [[stack]] e é gerenciada pelo sistema operacional, com custo alto de criação e comutação. Em contrapartida, o [[Go]] abstrai esse conceito com as [[goroutines]], que são como threads miniatura gerenciadas pelo runtime da linguagem, muito mais leves e eficientes para tarefas concorrentes. Mesmo assim, entender o conceito de thread continua sendo útil, porque é a base sobre a qual a concorrência moderna foi construída.

Cada thread, no contexto de programação, tem a sua **própria [[call stack]]**. Isso significa que quando você cria uma nova thread, o sistema operacional ou o runtime da linguagem aloca uma nova região de memória exclusiva pra ela, onde vai empilhar as chamadas de função feitas dentro dessa thread. Essa pilha é isolada das demais threads: se duas threads estiverem rodando ao mesmo tempo e ambas chamarem uma função chamada `processarDados()`, cada uma delas vai ter a _sua própria versão empilhada_ de `processarDados()` na sua respectiva stack. Elas não brigam pela mesma memória porque estão cada uma vivendo seu mundinho solitário de execução.

A stack da thread é onde ficam armazenados os dados temporários da execução: parâmetros de função, variáveis locais e o endereço de retorno — ou seja, pra onde voltar quando a função acabar. Quando uma função é chamada, o frame é empilhado na stack da thread atual. Quando ela retorna, o frame é removido. É uma dança organizada de entrada e saída de contextos de execução, sempre seguindo o modelo [[LIFO]]: a última chamada é a primeira a sair.

Agora, por que isso importa? Porque se você fizer besteira dentro de uma thread — tipo criar recursão infinita ou entupir a execução com chamadas profundas — _só a stack daquela thread vai explodir_. As outras threads, felizes e inconscientes, continuam rodando como se nada tivesse acontecido.

E em Go? A coisa muda um pouco, porque Go não usa threads diretamente. Ele usa goroutines, que são programaticamente muito mais leves, mas o princípio é parecido: **cada goroutine tem sua própria stack separada**, e ela funciona da mesma forma que a stack de uma thread — só que com o charme extra de ser pequena, flexível e controlada pelo runtime em vez do sistema operacional.

###### Go Scheduler


No Go, o agendador de goroutines — chamado carinhosamente de **Go scheduler** — é o componente do runtime responsável por decidir qual goroutine roda em qual thread, e quando. E aqui vai o plot twist: o Go _não_ cria uma thread nova do sistema operacional pra cada goroutine. Se fizesse isso, seria como contratar um funcionário novo pra cada planilha que você abre — caro, lento, e só útil se você quiser falir seu computador.

Em vez disso, o Go cria um pequeno grupo de **threads reais** (do sistema operacional), chamadas de **M** (de _machine threads_), e distribui as goroutines entre elas. Essas goroutines são o que chamamos de _green threads_ ou _user-space threads_ — elas são gerenciadas pelo runtime do Go, não pelo SO. Cada M é associada a uma **P**, que significa _processor_, que é uma unidade lógica do Go usada pra controlar acesso à execução. Então temos um esquema M:N: _M_ goroutines rodando em _N_ threads reais, sob a supervisão de um número fixo de _P_.

Quer um exemplo? Suponha que seu programa tenha `GOMAXPROCS=4`. Isso quer dizer que o runtime do Go vai criar 4 **P** — ou seja, vai usar até 4 núcleos da CPU simultaneamente. Essas P's são os “slots de execução”. Cada uma pode rodar goroutines, empilhando e desempilhando chamadas dentro da stack exclusiva da goroutine, e tudo isso é coordenado pelos threads **M**, que são responsáveis por pegar goroutines e executar, alternando entre elas conforme necessário.

O agendador decide _quando_ uma goroutine deve parar de rodar e outra deve assumir o controle. Isso acontece, por exemplo, quando uma goroutine faz uma chamada bloqueante (como I/O), ou quando ela excede um tempo limite de execução. Nesse momento, o scheduler guarda o estado da goroutine (inclusive a stack!) e coloca outra no lugar, como quem troca um funcionário sonolento por outro animado na linha de produção.

O mais divertido (ou caótico, se você for o tipo de dev que gosta de controle absoluto) é que tudo isso é **transparente**. Você escreve um código com `go minhaFuncao()`, e de repente você tem mil goroutines rodando “em paralelo”, mesmo que elas estejam dividindo umas poucas threads reais por trás das cortinas. Cada goroutine mantém sua própria stack, pequena e dinâmica, e o runtime gerencia o show — crescendo stacks, trocando contextos e fazendo você parecer um gênio da concorrência mesmo sem saber quantos `M`, `P` e `G` estão envolvidos.

##### **Como o Go lida com preempção**, ou seja, como ele lida com aquelas goroutines folgadas que decidem nunca mais sair da CPU?

Preempção é o ato do sistema tirar uma thread ou processo da CPU à força pra dar lugar a outro. Como um segurança educado (ou nem tanto) dizendo “ok, já deu, hora de vazar, meu chapa”. Em linguagens com agendamento _preemptivo_, o runtime ou o sistema operacional decide quando algo rodou demais e precisa ser interrompido. Já no _agendamento cooperativo_ (como o Go fazia nos tempos da caverna), a goroutine só saía da CPU quando ela _voluntariamente_ fazia uma chamada que permitisse o agendamento, tipo uma operação de I/O ou um `time.Sleep()`. Traduzindo: se uma goroutine entrasse num loop eterno sem nenhuma chamada de sistema ou função bloqueante, ela podia prender o processador e fazer todas as outras goroutines esperarem por ela. Egoísta e inconveniente.

A partir da versão 1.14, o Go adicionou **preempção assíncrona** ao seu agendador. Isso significa que agora o runtime consegue interromper goroutines no meio da execução, mesmo que elas não estejam cooperando. Como? Com um leve toque de bruxaria e um abuso bem pensado do **sinal de interrupção no tempo de execução**.

O compilador do Go injeta pequenas verificações nos loops e nas chamadas de função que ficam de olho em um sinal interno. Se o scheduler quiser interromper uma goroutine (porque ela tá rodando há tempo demais), ele marca isso com um sinalzinho. Na próxima verificação injetada, a goroutine vê o sinal e coopera: ela salva seu estado e dá lugar pra outra. É como se o Go tivesse enfiado um fiscal invisível dentro do seu código, que aparece de tempos em tempos e diz “Ei boy, vamo dar espaço pros outros brincar também?”

Essa verificação é leve e não aparece pra você, mas permite que o agendador recupere o controle do tempo de CPU e mantenha o sistema responsivo mesmo quando você escreveu um loop que ignora solenemente o resto do universo. O resultado é um sistema de concorrência mais justo e equilibrado, onde uma goroutine abusada não pode mais monopolizar os recursos como se tivesse comprado tudo à vista.