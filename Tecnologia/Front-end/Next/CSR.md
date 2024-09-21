---
tags:
  - back-end
  - front-end
  - Tecnologia
---
### **CSR - Client-Side Rendering (Renderização no Lado do Cliente):**

**O que é?**

A **renderização no lado do cliente** é o método onde o navegador do usuário é responsável por renderizar o conteúdo da página usando JavaScript. Inicialmente, o servidor envia uma página HTML básica, e o JavaScript carrega e gera o conteúdo dinamicamente.

**Como funciona?**

- O servidor envia um HTML mínimo com links para arquivos JavaScript.
- O navegador baixa e executa o JavaScript, que então constrói a interface do usuário.
- Dados adicionais são buscados via APIs conforme necessário.

**Vantagens:**

- **Interatividade Rica**: Aplicações altamente dinâmicas e responsivas.
- **Menor Carga no Servidor**: O servidor apenas serve arquivos estáticos.

**Desvantagens:**

- **SEO Prejudicado**: Motores de busca podem ter dificuldade em indexar conteúdo renderizado no cliente.
- **Tempo de Carregamento Inicial Mais Lento**: O usuário pode ver uma página em branco até que o JavaScript seja carregado e executado.
- **Dependência de JavaScript**: Se o JavaScript falhar ou estiver desabilitado, o site não funciona corretamente.