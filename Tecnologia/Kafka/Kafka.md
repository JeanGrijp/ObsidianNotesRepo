---
tags:
  - Tecnologia
  - SoftwareArchitecture
  - back-end
  - Aprendizagem
  - Produtividade
  - Security
---
# O que é kafka?

O **Apache Kafka** é uma plataforma distribuída de _streaming_ de eventos, projetada para lidar com grandes volumes de dados em tempo real. Ele nasceu dentro do LinkedIn e foi doado à Apache Foundation. O Kafka é usado principalmente para construir pipelines de dados e sistemas de _event-driven architecture_ — ou seja, sistemas que reagem a eventos à medida que eles acontecem.

Em vez de armazenar dados em tabelas como um banco tradicional, o Kafka trabalha com **tópicos**, que são como canais nomeados para onde os dados são publicados. Esses dados, chamados de **eventos** ou **mensagens**, são produzidos por **producers** (produtores) e consumidos por **consumers** (consumidores). Um tópico pode ter múltiplas **partições**, o que permite a escalabilidade horizontal e o processamento paralelo.

O Kafka é otimizado para ser extremamente rápido, resiliente e tolerante a falhas. Ele mantém os dados em disco de forma ordenada e imutável por um tempo determinado, o que possibilita reprocessar eventos passados sem nenhum problema. Além disso, ele permite que vários consumidores leiam o mesmo dado sem interferência, pois o controle da posição de leitura (offset) é gerenciado por cada consumidor.

Ele funciona geralmente com o apoio do **Zookeeper** — um serviço que mantém o estado dos brokers (servidores Kafka), como o controle de qual broker está ativo, qual é o líder de cada partição, entre outros metadados. A partir da versão 2.8, o Kafka passou a oferecer a opção de rodar sem o Zookeeper, com o modo chamado **KRaft (Kafka Raft Metadata Mode)**, que ainda está em evolução.

Resumidamente, o Kafka é como um correio entre sistemas: produtores colocam cartas (eventos) em caixas (tópicos), e os consumidores pegam as cartas para reagir ou processar como quiserem. Ele resolve o problema de integração entre sistemas desacoplados, onde a produção e o consumo de dados não precisam acontecer ao mesmo tempo, nem no mesmo ritmo.

### Mensageria assíncrona

Mensageria assíncrona é um padrão de comunicação entre sistemas ou componentes de software em que o remetente envia uma mensagem e não espera uma resposta imediata. Ao invés disso, a mensagem é colocada em uma fila ou tópico, e o destinatário pode processá-la posteriormente, de forma independente. Isso traz várias vantagens, como **desacoplamento entre serviços**, **resiliência**, **escalabilidade** e **melhor tolerância a falhas**.

Por exemplo, imagine um e-commerce. Quando um usuário finaliza a compra, o sistema pode:
1. Processar o pagamento imediatamente.
2. Enviar uma mensagem para uma fila com os dados da compra.
3. Um outro serviço, de forma assíncrona, consome essa mensagem e envia o e-mail de confirmação ou atualiza o estoque.

Esse modelo reduz o tempo de resposta para o cliente e evita acoplamento rígido entre os sistemas. Tecnologias como **Kafka**, **RabbitMQ**, **Amazon SQS**, e **Azure Service Bus** são ferramentas populares para implementar mensageria assíncrona.


### Produtor, consumidor, fila e tópico

**Produtor** é a aplicação ou serviço responsável por **enviar mensagens** para um sistema de mensageria. Ele não precisa saber quem vai ler a mensagem ou quando isso acontecerá. Sua única função é produzir (gerar) eventos ou dados e publicá-los em um **tópico**.

**Consumidor** é a aplicação ou serviço que **recebe as mensagens**. Ele se conecta ao sistema de mensageria, assina um tópico (ou escuta uma fila, dependendo da tecnologia) e consome as mensagens quando estiver pronto. Em Kafka, os consumidores podem ser organizados em **grupos**, o que facilita o processamento paralelo de mensagens.

Agora, sobre **fila** e **tópico**, a diferença vem principalmente do modelo adotado:

- **Fila** é uma estrutura usada em sistemas como **RabbitMQ** ou **Amazon SQS**. Nela, as mensagens seguem um modelo tradicional **FIFO (First In, First Out)**: o primeiro consumidor que pegar a mensagem é o único que a processa. Se você tem 3 consumidores escutando a mesma fila, cada mensagem vai para **apenas um** deles — é uma divisão de carga (load balancing).
- **Tópico**, usado por sistemas como **Apache Kafka**, funciona de forma mais flexível. Quando um produtor publica uma mensagem em um tópico, **vários consumidores** (de grupos diferentes) podem receber e processar a mesma mensagem. Isso é muito útil quando você tem múltiplos sistemas que precisam reagir ao mesmo evento — por exemplo, um serviço de notificação e um serviço de análise de dados, ambos ouvindo o mesmo tópico.


### Modelo pub/sub

O modelo **pub/sub** (publicador/assinante) é um padrão de comunicação assíncrona muito utilizado em sistemas distribuídos. Nele, os componentes que produzem informações — chamados de publicadores — enviam mensagens para um canal intermediário, conhecido como _tópico_. Por outro lado, os componentes interessados em determinadas informações — os assinantes — se inscrevem nesses tópicos e passam a receber automaticamente todas as mensagens publicadas neles. Essa estrutura permite o desacoplamento entre quem envia e quem consome as mensagens, ou seja, o publicador não precisa saber quem são os assinantes, e vice-versa.

Esse modelo é ideal para aplicações escaláveis e reativas, como sistemas de notificação, logs em tempo real, ou comunicação entre microserviços. No contexto do Kafka, os tópicos atuam como esse meio de comunicação central. Os produtores enviam mensagens para um tópico, e os consumidores que estiverem inscritos nesse tópico recebem as mensagens. Isso promove alta flexibilidade, já que vários consumidores podem ler a mesma mensagem de forma independente, possibilitando múltiplas reações a um mesmo evento, como atualizar um banco de dados, enviar um e-mail ou acionar um fluxo de trabalho.

### Durabilidade e persistência

No Kafka, **durabilidade** refere-se à capacidade de manter os dados mesmo diante de falhas. Quando uma mensagem é produzida, ela não é simplesmente transmitida aos consumidores e esquecida — ela é **persistida em disco**, ou seja, armazenada de forma que continue disponível mesmo que o broker ou o sistema inteiro sofra uma queda. Isso é possível porque o Kafka usa o sistema de arquivos de forma eficiente, gravando as mensagens em logs segmentados e sequenciais no disco.

Já a **persistência** garante que essas mensagens fiquem registradas por um determinado tempo (definido pela configuração do `retention.ms`), mesmo depois de serem lidas pelos consumidores. Isso difere de sistemas baseados puramente em filas, onde a leitura geralmente remove a mensagem do sistema. No Kafka, o consumidor mantém seu próprio controle sobre o ponto de leitura, chamado de _offset_, permitindo múltiplos consumidores lerem a mesma mensagem em momentos diferentes.

Além disso, o Kafka pode ser configurado com **fatores de replicação**. Isso significa que cada mensagem pode ser copiada para outros brokers no cluster, garantindo que, mesmo se um nó cair, as mensagens não serão perdidas. Essa abordagem fortalece tanto a durabilidade quanto a alta disponibilidade.

### Escalabilidade horizontal

A **escalabilidade horizontal** no contexto do Kafka é uma das suas características mais poderosas e é o que o torna ideal para sistemas de mensageria de alto desempenho e alta disponibilidade. Em vez de depender de um único servidor potente (escalabilidade vertical), o Kafka permite adicionar mais **nós (brokers)** ao cluster para distribuir a carga e aumentar a capacidade de processamento.

Essa escalabilidade é possível graças ao conceito de **partições**. Cada _tópico_ no Kafka pode ser dividido em várias partições, e essas partições podem ser distribuídas entre diferentes brokers. Quando um produtor envia mensagens para um tópico, essas mensagens são distribuídas entre as partições, seja por round-robin, por chave (hash), ou manualmente. Isso significa que múltiplos produtores podem escrever para diferentes partições ao mesmo tempo, e múltiplos consumidores também podem ler de partições diferentes simultaneamente, o que aumenta o throughput total do sistema.

Além disso, o Kafka permite agrupar consumidores em **consumer groups**, onde cada instância de consumidor do grupo recebe uma partição diferente do tópico. Isso significa que a leitura é feita de forma paralela entre os consumidores, garantindo uma escalabilidade eficiente na leitura também.

Outro ponto é que, como cada broker no cluster gerencia um subconjunto das partições, a carga é naturalmente balanceada. Quando você adiciona um novo broker ao cluster, as partições podem ser redistribuídas, aumentando a capacidade total sem interrupção no serviço. Essa flexibilidade é uma das razões pelas quais o Kafka é amplamente usado em sistemas que precisam crescer sem afetar a performance.

### Backpressure e throughput

O conceito de **backpressure** está relacionado ao controle de fluxo em sistemas de mensageria assíncrona como o Kafka. Ele ocorre quando um componente do sistema (geralmente o consumidor ou o broker) não consegue acompanhar o ritmo de entrada das mensagens — ou seja, os dados estão chegando mais rápido do que podem ser processados ou armazenados. Isso pode levar a acúmulo em memória, degradação da performance ou até falhas, se não houver mecanismos apropriados de controle.

No Kafka, o backpressure pode acontecer em diferentes pontos. Por exemplo, se um **produtor** estiver enviando mensagens muito rapidamente e o **broker** estiver sobrecarregado ou indisponível para escrita, o produtor pode ficar bloqueado ou receber erros de "buffer cheio". Da mesma forma, se um **consumidor** estiver lendo de forma lenta, as mensagens se acumulam no tópico e nas partições, o que pode gerar latência na entrega e pressão sobre o armazenamento do Kafka. Por isso, é fundamental monitorar métricas como o "lag" do consumidor — que indica o quão atrasado ele está em relação ao final da fila.

Já o **throughput** diz respeito à capacidade total do sistema de processar mensagens por segundo, e é um dos principais objetivos de otimização em sistemas baseados em Kafka. A arquitetura distribuída do Kafka, baseada em múltiplos brokers e partições, é pensada para maximizar o throughput horizontalmente. O throughput também depende de fatores como o tamanho das mensagens, a configuração dos buffers, o uso de compressão, o número de threads em consumidores e produtores, e a eficiência da rede.

Controlar o backpressure é essencial para manter um bom throughput. Isso pode ser feito, por exemplo, ajustando o tamanho dos lotes de mensagens enviados (`batch.size`), o tempo de espera para envio (`linger.ms`), e o uso de **ACKs** para garantir que o Kafka não se sobrecarregue. Em consumidores, o paralelismo (vários consumidores em um grupo) ajuda a aumentar o throughput de leitura e evitar o acúmulo de mensagens nas filas.

