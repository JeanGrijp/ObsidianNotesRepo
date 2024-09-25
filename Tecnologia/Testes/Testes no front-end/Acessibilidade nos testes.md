---
tags:
  - Tecnologia
  - front-end
  - testes
---
###### **Como Fazer Query no Teste Usando a Função da Tag (Role) em Vez do Nome do Elemento**

No React Testing Library, você pode buscar elementos no DOM utilizando seus papéis (roles) acessíveis, em vez dos nomes específicos dos elementos HTML. Isso é especialmente útil para escrever testes que refletem melhor como os usuários interagem com sua aplicação, promovendo a acessibilidade e a manutenção do código.

### Usando `getByRole` para Selecionar Elementos por Função (Role)

Os elementos de cabeçalho (`<h1>` a `<h6>`) têm o papel (role) acessível de **`heading`**. Você pode utilizar o método `getByRole` para selecionar esses elementos em seus testes.

#### Exemplo Básico

**Componente:**
```jsx
// Componente.jsx
function Componente() {
  return (
    <div>
      <h1>Título Principal</h1>
      <h2>Subtítulo</h2>
      <p>Parágrafo de texto.</p>
    </div>
  );
}

export default Componente;

```

Teste:
```jsx
// Componente.test.jsx
import { render, screen } from '@testing-library/react';
import Componente from './Componente';

test('deve renderizar os cabeçalhos corretamente', () => {
  render(<Componente />);

  // Seleciona todos os elementos com role "heading"
  const headings = screen.getAllByRole('heading');
  expect(headings).toHaveLength(2);

  // Seleciona o h1 específico
  const h1Heading = screen.getByRole('heading', { level: 1 });
  expect(h1Heading).toHaveTextContent('Título Principal');

  // Seleciona o h2 específico
  const h2Heading = screen.getByRole('heading', { level: 2 });
  expect(h2Heading).toHaveTextContent('Subtítulo');
});
```

#### Explicação:

- **`getByRole('heading')`**: Seleciona elementos que têm o role "heading", o que inclui todos os `<h1>` a `<h6>`.
- **Opção `{ level: número }`**: Permite especificar o nível do heading (1 para `<h1>`, 2 para `<h2>`, etc.).
- **`getAllByRole('heading')`**: Retorna um array de todos os elementos que correspondem ao role "heading".

### Por Que Usar `getByRole`?

Usar `getByRole` é uma prática recomendada porque:

- **Acessibilidade**: Reflete como tecnologias assistivas interpretam sua interface.
- **Confiabilidade**: Menos propenso a quebras com alterações no código, desde que o role permaneça o mesmo.
- **Semântica**: Incentiva o uso correto de elementos HTML semânticos.

### Exemplo com Outros Elementos

**Componente:**
```jsx
function ComponenteComVariadosElementos() {
  return (
    <div>
      <h1>Meu Título</h1>
      <button>Enviar</button>
      <a href="/sobre">Sobre Nós</a>
      <input type="text" placeholder="Digite seu nome" />
    </div>
  );
}

export default ComponenteComVariadosElementos;
```

Teste:
```jsx
import { render, screen } from '@testing-library/react';
import ComponenteComVariadosElementos from './ComponenteComVariadosElementos';

test('deve renderizar todos os elementos corretamente', () => {
  render(<ComponenteComVariadosElementos />);

  // Seleciona o heading
  const heading = screen.getByRole('heading', { level: 1 });
  expect(heading).toHaveTextContent('Meu Título');

  // Seleciona o botão
  const button = screen.getByRole('button', { name: /enviar/i });
  expect(button).toBeInTheDocument();

  // Seleciona o link
  const link = screen.getByRole('link', { name: /sobre nós/i });
  expect(link).toBeInTheDocument();

  // Seleciona a caixa de texto
  const textbox = screen.getByRole('textbox');
  expect(textbox).toBeInTheDocument();
});
```

### Como Encontrar o Role de um Elemento

Se você não tiver certeza do role associado a um elemento, pode:

- Consultar a [lista de roles acessíveis na MDN](https://developer.mozilla.org/pt-BR/docs/Web/Accessibility/ARIA/Roles).
- Usar ferramentas de desenvolvimento do navegador para inspecionar a acessibilidade (por exemplo, o painel "Accessibility" nas DevTools do Chrome).
- Referenciar a [documentação do ARIA](https://www.w3.org/TR/wai-aria-1.1/#role_definitions).

###### **Combinação com Nome**

Você pode tornar sua query mais específica combinando o role com o nome acessível do elemento.

```jsx
const heading = screen.getByRole('heading', { name: /meu título/i });
```

###### **Roles Comuns**:

- **`button`**: `<button>`, `<input type="button">`, `<input type="submit">`
- **`link`**: `<a href="">`, `<area>`, `<link>`
- **`textbox`**: `<input type="text">`, `<textarea>`, `<input type="email">`
- **`checkbox`**: `<input type="checkbox">`
- **`radio`**: `<input type="radio">`
- **`heading`**: `<h1>` a `<h6>`

###### Usando [ReGex](ReGex.md)

```jsx
const button = screen.getByRole('button', { name: /enviar|submit/i });
```

**Case Insensitive**: A flag `i` nas expressões regulares torna a busca case-insensitive.

### Exemplo com Níveis de Heading

Se você tiver vários headings de diferentes níveis e quiser testar cada um especificamente:

**Componente:**
```jsx
function Artigo() {
  return (
    <article>
      <h1>Título do Artigo</h1>
      <section>
        <h2>Seção 1</h2>
        <p>Conteúdo da seção 1.</p>
      </section>
      <section>
        <h2>Seção 2</h2>
        <p>Conteúdo da seção 2.</p>
      </section>
    </article>
  );
}

export default Artigo;
```

Teste:
```jsx
import { render, screen } from '@testing-library/react';
import Artigo from './Artigo';

test('deve renderizar o artigo com headings corretos', () => {
  render(<Artigo />);

  // Verifica o h1
  const h1Heading = screen.getByRole('heading', { level: 1 });
  expect(h1Heading).toHaveTextContent('Título do Artigo');

  // Verifica todos os h2
  const h2Headings = screen.getAllByRole('heading', { level: 2 });
  expect(h2Headings).toHaveLength(2);
  expect(h2Headings[0]).toHaveTextContent('Seção 1');
  expect(h2Headings[1]).toHaveTextContent('Seção 2');
});
```

### Lidando com Elementos Semânticos Personalizados

Se você estiver usando elementos personalizados ou estilizando elementos de maneira que não mantenham seus roles acessíveis padrão, considere adicionar atributos ARIA para especificar o role.

**Componente com Elemento Personalizado:**
```jsx
function TituloPersonalizado() {
  return <div role="heading" aria-level="1">Título Personalizado</div>;
}

export default TituloPersonalizado;
```

Teste:
```jsx
test('deve renderizar o título personalizado como heading', () => {
  render(<TituloPersonalizado />);

  const heading = screen.getByRole('heading', { level: 1 });
  expect(heading).toHaveTextContent('Título Personalizado');
});
```

**Nota:** Usar elementos semânticos HTML padrão é preferível para acessibilidade. Utilize roles [[ARIA]] somente quando não for possível usar os elementos apropriados.