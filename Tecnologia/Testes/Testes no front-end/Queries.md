---
tags:
  - testes
  - Tecnologia
  - front-end
---
## Métodos de Query no React Testing Library

O React Testing Library fornece uma variedade de métodos para buscar elementos no DOM:

- **getBy**: Retorna o elemento se encontrado. Lança um erro se não for encontrado.
- **queryBy**: Retorna o elemento se encontrado. Retorna `null` se não for encontrado.
- **findBy**: Retorna uma Promise que resolve quando o elemento é encontrado. Útil para testes assíncronos.

Esses métodos podem ser combinados com diferentes seletores:

- **getByText**: Seleciona elementos pelo texto visível.
- **getByRole**: Seleciona elementos pelo seu papel (role) acessível.
- **getByLabelText**: Seleciona elementos associados a um `<label>`.
- **getByPlaceholderText**: Seleciona elementos pelo atributo `placeholder`.
- **getByAltText**: Seleciona elementos pelo texto alternativo (atributo `alt`).
- **getByDisplayValue**: Seleciona elementos pelo valor exibido (útil para inputs).

## Práticas Recomendadas para Buscar Elementos

A ideia central é buscar elementos da forma mais próxima possível de como o usuário interage com eles. Isso aumenta a confiabilidade dos testes e melhora a acessibilidade das aplicação.

###### OBS
Todas as buscas por elementos no DOM, devem ser feitas com [Acessibilidade nos testes](Acessibilidade%20nos%20testes.md).

### Ordem de Preferência para Queries

1. **getByRole**
2. **getByLabelText**
3. **getByPlaceholderText**
4. **getByText**
5. **getByDisplayValue**
6. **getByAltText**
7. **getByTestId**

Evite depender de `getByTestId` a menos que não haja outra maneira de selecionar o elemento.

### Usando `getByRole`

O `getByRole` é altamente recomendado porque reflete como tecnologias assistivas percebem a página.

```tsx
function SubmitButton() {
  return <button type="submit">Enviar</button>;
}
```

Teste:

```tsx
import { render, screen } from '@testing-library/react';
import SubmitButton from './SubmitButton';

test('renderiza o botão de envio', () => {
  render(<SubmitButton />);
  const buttonElement = screen.getByRole('button', { name: /enviar/i });
  expect(buttonElement).toBeInTheDocument();
});
```

A [ReGex](ReGex.md) em /enviar/i é para ignorar a diferença entre maiúsculas e minúsculas.

### Usando `getByLabelText`

Útil para inputs associados a labels.

**Exemplo:**

```tsx
function LoginForm() {
  return (
    <form>
      <label htmlFor="username">Usuário</label>
      <input id="username" />
    </form>
  );
}
```

Teste:

```tsx
import { render, screen } from '@testing-library/react';
import LoginForm from './LoginForm';

test('renderiza o campo de usuário', () => {
  render(<LoginForm />);
  const inputElement = screen.getByLabelText(/usuário/i);
  expect(inputElement).toBeInTheDocument();
});
```

### Usando `getByPlaceholderText`

Quando o input possui um placeholder.

**Exemplo:**

```tsx
function SearchBar() {
  return <input placeholder="Buscar..." />;
}
```

Teste:

```tsx
import { render, screen } from '@testing-library/react';
import SearchBar from './SearchBar';

test('renderiza a barra de busca', () => {
  render(<SearchBar />);
  const inputElement = screen.getByPlaceholderText(/buscar/i);
  expect(inputElement).toBeInTheDocument();
});
```

### Usando `getByText`

Para elementos cujo conteúdo textual é único.

**Exemplo:**

```
function WelcomeMessage() {
  return <h1>Bem-vindo ao nosso site!</h1>;
}
```

Teste:

```tsx
import { render, screen } from '@testing-library/react';
import WelcomeMessage from './WelcomeMessage';

test('renderiza a mensagem de boas-vindas', () => {
  render(<WelcomeMessage />);
  const headingElement = screen.getByText(/bem-vindo ao nosso site!/i);
  expect(headingElement).toBeInTheDocument();
});
```

### Usando `getByAltText`

Para imagens com atributo `alt`.

**Exemplo:**

```tsx
function UserProfile() {
  return <img src="profile.jpg" alt="Foto de perfil do usuário" />;
}
```

Teste:

```tsx
import { render, screen } from '@testing-library/react';
import UserProfile from './UserProfile';

test('renderiza a imagem de perfil', () => {
  render(<UserProfile />);
  const imgElement = screen.getByAltText(/foto de perfil do usuário/i);
  expect(imgElement).toBeInTheDocument();
});
```

## Dicas Adicionais

**Usar Regex e Flags:** Ao usar métodos como `getByText`, você pode usar expressões regulares com a flag `i` para tornar a busca case-insensitive.

```tsx
screen.getByText(/texto a buscar/i);
```

**Combinação de Seletores:** Às vezes, pode ser necessário combinar seletores para alcançar um elemento específico.

```tsx
screen.getByRole('button', { name: /enviar/i });
```

**Esperar por Elementos Assíncronos:** Use `findBy` quando o elemento pode não estar imediatamente disponível (por exemplo, após uma chamada de API).

```tsx
const dataElement = await screen.findByText(/dados carregados/i);
```

**Verificar a Não Existência de Elementos:** Use `queryBy` para verificar que um elemento não está presente.

```tsx
const errorElement = screen.queryByText(/erro/i);
expect(errorElement).toBeNull();
```

**Test IDs:** Se nenhuma das opções acima funcionar, você pode adicionar um atributo `data-testid` ao elemento.

**Exemplo:**

```tsx
<div data-testid="custom-element">Conteúdo</div>
```

Teste:

```tsx
const customElement = screen.getByTestId('custom-element');
```

**Nota:** O uso de `data-testid` deve ser o último recurso.

- Priorizar métodos que refletem interações reais do usuário.
- Escrever testes claros e legíveis.
- Evitar dependências excessivas em seletores frágeis ou implementações internas.