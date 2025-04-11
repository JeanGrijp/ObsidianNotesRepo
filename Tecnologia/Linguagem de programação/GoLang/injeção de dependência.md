# Introdução

Quando trabalhamos com desenvolvimento de software em uma empresa, geralmente colaboramos com outras pessoas, e com isso surgem várias questões que devemos nos preocupar para manter as coisas organizadas no longo prazo. E se não nos esforçarmos para manter as coisas coesas, com o tempo a entropia vai cuidar de deixar a desorganização matar o seu trabalho.

Essa desorganização ocorre pelo fato de que pessoas pensam diferente e isso pode afetar as coisas mais simples. Por exemplo, imagine que você precise adicionar um novo _endpoint_ que altera o estado do banco de dados de uma aplicação. Pode parecer um simples cenário, mas mesmo para esse exemplo, algumas perguntas podem surgir se você não tiver regras bem claras de como fazer as coisas:

- Onde devo colocar o novo código do _endpoint_?
- Devo reutilizar algum modelo preexistente?
- Posso utilizar o modelo de leitura do banco de dados para retornar os dados da _API_?
- Posso passar a _querystring_ do _REST_ para dentro dos serviços?

A princípio, essas perguntas podem parecer triviais, mas quando se trabalha em equipe, é importante ter algumas regras de desenvolvimento. A utilização de uma arquitetura de software nos ajuda a estabelecer essas regras e a ter um software mais coeso. Se você ler [_Domain-Driven Design_](https://www.amazon.com.br/Domain-Driven-Design-Atacando-Complexidades-Software/dp/8550800651), verá que para aplicar _DDD_ é obrigatório isolar o domínio em algo que Eric Evans chama de Arquitetura em Camadas. Se você ler [Arquitetura Limpa](https://www.amazon.com.br/Arquitetura-Limpa-Artes%C3%A3o-Estrutura-Software/dp/8550804606), do _Uncle Bob_, verá que ele define uma série de princípios relacionados a coesão e acoplamento, e que a arquitetura proposta por ele auxilia em garantir alta coesão, baixo acoplamento e elevar a capacidade de reutilização.

Quando falamos em arquitetura de software, estamos basicamente falando em definir uma série de regras sobre como os arquivos, pastas, módulos, pacotes de uma aplicação se relacionam entre si. E isso é algo muito importante quando trabalhamos em grandes aplicações junto a um time. Quando Alistair Cockburn propôs pela primeira vez a [Arquitetura Hexagonal](https://alistair.cockburn.us/hexagonal-architecture/) em 2005, ele tinha em mente conceitos de _DDD_ e essa arquitetura é uma opção a Arquitetura Limpa.

Para quem quiser ver o próprio Alistair explicando os conceitos por trás da Arquitetura Hexagonal, aqui vai uma excelente referencia.