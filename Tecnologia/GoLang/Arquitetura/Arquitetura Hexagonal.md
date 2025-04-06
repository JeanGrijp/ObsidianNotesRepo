---
tags:
  - Tecnologia
  - GoLang
  - back-end
  - Produtividade
  - Database
---
A arquitetura Hexagonal é uma forma de organizar o código em camadas, também conhecida como *adapters and ports*, ela tem o objetivo de isolar complemente a aplicação do mundo externo. Esse isolamento é feito por meio de portas e adaptadores, onde as portas são as interfaces que as camadas de baixo nível expõe, e Adaptadores as implementações para as interfaces em questão.

![](Screenshot%202025-04-05%20212644.png)

Com isso, a aplicação pode se comunicar com diversos meios externos, como bancos de dados, filas de mensagens, serviços HTTP ou SMTP, sem depender diretamente de bibliotecas ou tecnologias específicas. Ao invés disso, essas dependências são invertidas: o núcleo da aplicação define o **contrato** (interface), e o mundo externo fornece a implementação, permitindo maior testabilidade, manutenção e flexibilidade.

No centro da arquitetura está o **Domínio**, que contém as entidades, eventos e interfaces dos repositórios — tudo aquilo que representa as regras de negócio puras. Em volta do domínio está a camada de **Aplicação**, onde residem os casos de uso, responsáveis por orquestrar as regras de negócio. Finalmente, a camada mais externa é a de **Infraestrutura**, onde ficam os controladores HTTP, os adaptadores para banco de dados, serviços externos, envio de e-mails, etc.

Dessa forma, a aplicação pode evoluir, mudar de banco de dados, trocar ferramentas ou até reescrever completamente a interface externa sem afetar o coração da lógica de negócio. Essa abordagem segue os princípios da [[Inversão de Dependência]] e da [[Programação Orientada a Interfaces]], promovendo um código mais limpo, desacoplado e sustentável a longo prazo.

Na arquitetura Hexagonal, esses dois princípios trabalham lado a lado. O domínio da aplicação define as portas (interfaces) que representam os contratos essenciais com o mundo externo, como por exemplo repositórios, gateways de pagamento, serviços de envio de e-mail, etc. As camadas externas, por sua vez, implementam essas interfaces e se conectam ao restante do mundo real. A aplicação central **nunca sabe como as coisas são feitas, apenas que elas são feitas de acordo com o contrato estabelecido.**

Dessa forma, conseguimos isolar completamente as regras de negócio do restante do sistema, manter o código do domínio limpo e testável, e substituir ou adicionar novas tecnologias com pouquíssimo impacto. O sistema torna-se mais sustentável, preparado para mudanças e resistente a acoplamentos desnecessários.