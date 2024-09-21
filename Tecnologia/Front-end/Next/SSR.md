---
tags:
  - back-end
  - front-end
  - Tecnologia
---
### **SSR - Server-Side Rendering (Renderização no Lado do Servidor):**

**O que é?**

A **renderização no lado do servidor** é um método onde o servidor gera o HTML completo da página antes de enviá-lo ao cliente (navegador). Isso significa que o conteúdo da página é processado no servidor e enviado já pronto para o usuário.

**Como funciona?**

- Quando um usuário solicita uma página, o servidor processa essa solicitação.
- Executa todo o código necessário (incluindo a obtenção de dados).
- Retorna ao navegador um HTML completo e renderizado.

**Vantagens:**

- **Melhor SEO**: Como o conteúdo está presente no HTML inicial, os motores de busca podem indexar o conteúdo facilmente.
- **Tempo de Carregamento Inicial Mais Rápido**: O usuário vê o conteúdo mais rapidamente, pois a página já vem pronta.

**Desvantagens:**

- **Maior Carga no Servidor**: Cada solicitação requer processamento completo no servidor.
- **Latência**: Pode aumentar o tempo de resposta se o servidor estiver distante do usuário.