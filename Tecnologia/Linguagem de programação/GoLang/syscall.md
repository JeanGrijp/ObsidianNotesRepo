---
tags:
  - Tecnologia
  - SoftwareArchitecture
  - GoLang
---
Em [[Go]], uma **syscall** (system call) é uma chamada feita do código Go para o sistema operacional, solicitando que ele execute uma operação que não pode ser realizada diretamente pela aplicação, como leitura e escrita de arquivos, acesso à rede, manipulação de processos, gerenciamento de memória, entre outros. As syscalls são fundamentais para qualquer linguagem de programação, porque é através delas que o software interage com o hardware e recursos do sistema operacional de maneira controlada e segura.

No Go, as syscalls podem acontecer de forma explícita (usando o pacote `syscall`, que permite acesso direto a chamadas de sistema específicas) ou, mais comumente, de forma implícita, toda vez que você faz operações como abrir um arquivo, aceitar uma conexão de rede, ler um socket, ou esperar por um tempo (`time.Sleep`, por exemplo). Quase todas as operações de I/O em Go, mesmo as mais “simples” da biblioteca padrão, acabam envolvendo uma syscall por baixo dos panos.

O pacote `syscall` oferece uma interface de baixo nível para as syscalls do sistema operacional, mas na maioria dos casos, recomenda-se usar as abstrações mais seguras e idiomáticas da biblioteca padrão (`os`, `net`, `time`, etc.) em vez de trabalhar diretamente com ele. O uso direto do pacote `syscall` normalmente é restrito a casos muito específicos de integração com recursos de sistema que não estão expostos pela biblioteca padrão, ou para otimizações de baixo nível.

Quando uma [[goroutine]] faz uma syscall bloqueante (como uma leitura de rede ou arquivo), a thread do SO associada àquela goroutine ficará bloqueada até a operação terminar. Para evitar o gargalo, o [[runtime do Go]] faz aquele processo que já falamos antes: ele desacopla o “P” (processor) da thread bloqueada e associa a outro “M” ([[thread]]), para que outras goroutines possam seguir rodando. Isso garante que operações de I/O intensivas não prejudiquem a execução do restante do programa.

Além disso, o Go tem um mecanismo chamado **network poller**, responsável por monitorar operações de rede de forma assíncrona e eficiente. Em muitos casos, o runtime consegue evitar o bloqueio de threads ao utilizar chamadas de sistema não bloqueantes e multiplexação (epoll no Linux, kqueue no BSD/Mac, IOCP no Windows). Assim, várias operações de rede podem ser acompanhadas por poucas threads do SO, otimizando ainda mais o uso de recursos.

Por fim, se você precisa interagir com chamadas de sistema muito específicas, não expostas pelo pacote padrão, pode usar tanto o pacote `syscall` quanto o mais recente `golang.org/x/sys`, que é recomendado atualmente por ser mais seguro e atualizado. Em resumo, as syscalls em Go são parte essencial da interação da linguagem com o sistema operacional, mas a maior parte dos desenvolvedores raramente precisa lidar com elas diretamente, graças ao trabalho do runtime e das bibliotecas padrão que abstraem quase toda a complexidade.