---
tags:
  - back-end
  - front-end
  - Tecnologia
---
### **SSG - Static Site Generation (Geração Estática de Sites):**

**O que é?**

A **geração estática de sites** é um método onde as páginas são pré-renderizadas em HTML no momento da build (compilação) da aplicação. As páginas resultantes são arquivos estáticos que podem ser servidos rapidamente ao usuário.

**Como funciona?**

- Durante o processo de build, o sistema gera arquivos HTML estáticos para cada página.
- Esses arquivos podem ser servidos por uma CDN, reduzindo a latência.

**Vantagens:**

- **Desempenho Excelente**: Páginas estáticas são servidas rapidamente ao usuário.
- **Escalabilidade**: Menos carga no servidor, pois não há processamento dinâmico.
- **Segurança**: Menor superfície para ataques, já que não há execução de código no servidor por solicitação.

**Desvantagens:**

- **Conteúdo Estático**: Para atualizar o conteúdo, é necessário rebuildar o site.
- **Personalização Limitada**: Difícil implementar conteúdo altamente dinâmico ou personalizado.