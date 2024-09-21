---
tags:
  - front-end
  - Tecnologia
---
No [Next.js](Next.js.md), o roteamento é feito de maneira bem simples e automática através do sistema de **file-based routing**. Isso significa que o roteamento é baseado nos arquivos e pastas dentro do diretório `pages`. Cada arquivo ou pasta dentro de `pages` automaticamente se torna uma rota da aplicação.

Aqui está um resumo de como o roteamento funciona no Next.js:

### 1. Roteamento Básico:
Qualquer arquivo [JavaScript](JavaScript.md) ou [TypeScript](TypeScript.md) dentro da pasta `pages` se torna uma rota.

- Se você criar um arquivo `about.js` dentro de `pages`, ele será acessível através da rota `/about`.
- O arquivo `index.js` dentro de `pages` será a rota principal (`/`).

**Exemplo:**
```
pages/
  index.js      // Rota: /
  about.js      // Rota: /about
  contact.js    // Rota: /contact
```

### 2. Roteamento Aninhado:
Para criar rotas aninhadas, você pode organizar os arquivos dentro de pastas.

**Exemplo:**
```
pages/
  blog/
    index.js    // Rota: /blog
    post.js     // Rota: /blog/post
```

### 3. Rotas Dinâmicas:
Para rotas dinâmicas, você pode usar colchetes (`[]`) no nome do arquivo. Isso permite capturar segmentos dinâmicos da URL.

**Exemplo:**
- `pages/blog/[slug].js` pode ser acessado através de uma URL como `/blog/my-post`, onde `my-post` é o valor capturado pela variável `slug`.

No código do arquivo, você pode acessar esse valor dinâmico usando o **hook** `useRouter`:

```ts
import { useRouter } from 'next/router';

const Post = () => {
  const router = useRouter();
  const { slug } = router.query;  // "slug" será o valor dinâmico da URL

  return <p>Post: {slug}</p>;
}

export default Post;
```

### 4. Rotas API:
Next.js também permite criar rotas de API dentro da pasta `pages/api`. Cada arquivo dentro de `pages/api` se torna um endpoint.

**Exemplo:**
- `pages/api/user.js` será acessível em `/api/user`.

```ts
// pages/api/user.js
export default function handler(req, res) {
  res.status(200).json({ name: 'John Doe' });
}
```

### 5. Roteamento com `getStaticPaths` e `getStaticProps`:
Quando você está usando rotas dinâmicas com geração estática (SSG), é comum usar os métodos `getStaticPaths` para gerar as rotas dinâmicas no build e `getStaticProps` para fornecer os dados.

```ts
export async function getStaticPaths() {
  // Retorna as rotas dinâmicas que devem ser geradas estaticamente
  return {
    paths: [{ params: { slug: 'my-post' } }],
    fallback: false, // ou true/ blocking, dependendo da estratégia
  };
}

export async function getStaticProps({ params }) {
  // Aqui você obtém os dados para cada rota
  return {
    props: { postData: 'Dados do post' },
  };
}
```

Esse é o básico do roteamento no [Next.js](Next.js.md). Você tem bastante flexibilidade para criar rotas estáticas, dinâmicas, e endpoints de API diretamente no diretório `pages`.
