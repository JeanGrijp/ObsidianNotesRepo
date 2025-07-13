---
tags:
  - Tecnologia
  - GoLang
---
No [[Go]], o agendador de goroutines — chamado carinhosamente de **Go scheduler** — é o componente do runtime responsável por decidir **qual goroutine roda, em qual [[thread]], e quando**. E aqui vai o plot twist: o Go _não_ cria uma thread nova do sistema operacional pra cada [goroutine](Goroutine.md). Se fizesse isso, seria como contratar um funcionário novo pra cada planilha que você abre — caro, lento, e só útil se você quiser falir seu computador.

Em vez disso, o Go cria um pequeno grupo de **threads reais** (do sistema operacional), chamadas de **M** (_machine threads_), e distribui as goroutines entre elas. Essas goroutines são gerenciadas inteiramente pelo runtime da linguagem, o que as torna semelhantes às chamadas _green threads_ ou _user-space threads_ — embora com diferenças importantes: no Go, múltiplas goroutines são mapeadas para múltiplas threads, não apenas uma.

Cada `M` precisa estar associada a um `P`, de _Processor_, que é uma unidade lógica do Go usada para coordenar a execução de código Go. O modelo de execução é conhecido como **G:M:P**, onde:

- `G` é a goroutine (o que será executado),
    
- `M` é a thread do sistema operacional (o que executa),
    
- `P` é o processador lógico (o que permite que a execução ocorra).
    

Quer um exemplo? Suponha que seu programa tenha `GOMAXPROCS=4`. Isso quer dizer que o runtime do Go vai criar 4 **P** — ou seja, vai usar até 4 núcleos da CPU simultaneamente. Esses P’s são os “slots de execução”: cada `P` permite que uma `M` execute goroutines, empilhando e desempilhando chamadas dentro da stack exclusiva da goroutine. O runtime coordena essa execução, e as threads (`M`) vão pegando e executando as goroutines disponíveis.

O agendador decide _quando_ uma goroutine deve parar de rodar e outra deve assumir o controle. Isso acontece, por exemplo, quando uma goroutine realiza uma operação bloqueante (como I/O), ou atinge um **ponto seguro de preempção** — como loops longos ou chamadas a funções específicas. Nesse momento, o scheduler salva o estado da goroutine (inclusive a stack!) e coloca outra no lugar, como quem troca um funcionário sonolento por outro animado na linha de produção.

O mais divertido (ou caótico, se você for o tipo de dev que gosta de controle absoluto) é que tudo isso é **transparente**. Você escreve um código com `go minhaFuncao()`, e de repente tem mil goroutines rodando “em paralelo” — mesmo que elas estejam dividindo umas poucas threads reais por trás das cortinas. Cada goroutine mantém sua própria stack, **pequena e dinâmica**, e o runtime gerencia o show: cresce stacks sob demanda, troca contextos, e faz você parecer um gênio da concorrência mesmo sem saber quantos `M`, `P` e `G` estão trabalhando nos bastidores.

### Mas o que é esse tal de `P`?

No modelo de agendamento do Go, o `P` (de _Processor_) representa uma **unidade lógica de execução** controlada pelo runtime. Ele não é um thread nem uma goroutine, mas sim o **intermediário essencial** que gerencia o agendamento de goroutines e fornece contexto para que o código Go seja executado por uma thread (`M`). Cada `P` possui uma **fila local de goroutines prontas para execução**, conhecida como **run queue**. O runtime sempre tenta consumir essa fila local primeiro, o que melhora o desempenho ao evitar sincronização com outras estruturas globais e aproveita melhor o cache da CPU.

Quando uma thread (`M`) está associada a um `P`, ela pode executar goroutines da run queue daquele `P`. Caso essa fila esteja vazia, o runtime tenta buscar novas goroutines da **fila global**, ou até mesmo **roubar goroutines de outro `P`**, numa estratégia conhecida como **work stealing**. Esse mecanismo evita desequilíbrios entre processadores lógicos e mantém a carga distribuída mesmo em cenários de concorrência intensa.

O número de `P`s ativos é determinado por `GOMAXPROCS`, o que significa que, mesmo que existam milhares de goroutines, apenas um número limitado (igual ao de `P`s) pode estar executando código Go simultaneamente. As demais ficam na fila aguardando sua vez.

### Chamadas bloqueantes (syscalls) e uso inteligente de threads (`M`)

Em Go, uma goroutine pode, eventualmente, fazer uma chamada ao sistema operacional que bloqueia a thread. Isso acontece em situações comuns, como chamadas de rede, leitura de arquivos ou qualquer operação de I/O via [[syscall]]. Quando isso ocorre, a thread (`M`) que estava executando a goroutine **fica bloqueada** até que a operação termine.

Agora vem a parte crítica: se o runtime simplesmente deixasse o `P` associado a essa `M` bloqueada, o programa perderia a capacidade de executar novas goroutines naquele slot de execução — o que resultaria em desperdício de recursos e perda de desempenho. Para evitar isso, o Go runtime **desassocia o `P` da thread bloqueada (`M`)** e o entrega a outra thread que esteja disponível. Dessa forma, o `P` continua executando outras goroutines prontas na fila, e o programa segue rodando com eficiência, mesmo durante operações de I/O demoradas. Se não houver nenhuma `M` disponível no momento, o runtime pode **criar uma nova thread real** do sistema operacional. Isso é feito de maneira controlada, com limites internos, para evitar que o programa crie um número excessivo de threads e sobrecarregue o sistema.

Essa estratégia de **desacoplamento entre o agendamento das goroutines e o bloqueio do sistema operacional** é um dos principais motivos pelos quais o Go consegue lidar tão bem com aplicações altamente concorrentes — como servidores HTTP, workers de fila, ou qualquer sistema com múltiplas operações simultâneas que dependem de I/O. Além disso, é importante destacar que o Go diferencia bem **código Go puro** (que pode ser interrompido, agendado, migrado entre threads) de **código que envolve chamadas nativas ou externas**, onde o controle de execução passa, mesmo que temporariamente, para o SO.