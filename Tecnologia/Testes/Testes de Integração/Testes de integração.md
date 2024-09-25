---
tags:
  - Tecnologia
  - front-end
  - testes
aliases: []
---
Antes de começar, vale assistir ao vídeo do Felipe Rocha sobre testes: [Testes no React com Jest & Testing Library | Guia Completo](https://www.youtube.com/watch?v=0hVXjqHwvI0&ab_channel=FelipeRocha%E2%80%A2dicasparadevs)
###### **Como Realizar Testes de Integração com o React Testing Library**

Testes de integração são fundamentais para garantir que diferentes componentes da sua aplicação funcionem corretamente em conjunto. No contexto do React Testing Library, isso significa renderizar múltiplos componentes juntos e verificar se eles interagem como esperado.

## O Que São Testes de Integração?

Testes de integração verificam a interação entre diferentes partes do sistema. Ao contrário dos testes unitários, que isolam um componente para testar seu comportamento individual, os testes de integração certificam-se de que componentes ou módulos funcionam corretamente quando combinados.

###### **Exemplos de cenários para testes de integração:**

- Verificar se um componente pai passa corretamente props para seus componentes filhos.
- Testar fluxos de usuário que envolvem múltiplos componentes (por exemplo, um formulário com vários passos).
- Garantir que as chamadas de API sejam feitas corretamente e que os dados sejam exibidos como esperado.

## Escrevendo Testes de Integração

### Estrutura Básica de um Teste de Integração

Vamos supor que temos uma aplicação simples que consiste em um formulário de login. O formulário tem dois campos: email e senha, e um botão de submissão. Quando o usuário clica no botão, uma chamada à API é feita, e o usuário é redirecionado para uma página de dashboard.

**Componentes envolvidos:**

- `LoginForm`: Componente do formulário de login.
- `Dashboard`: Componente que mostra informações do usuário após o login.

### Exemplo Prático

**LoginForm.jsx**
```jsx
import React, { useState } from 'react';

function LoginForm({ onLoginSuccess }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    // Simula uma chamada à API
    const response = await fakeApiLogin(email, password);
    if (response.success) {
      onLoginSuccess();
    } else {
      alert('Credenciais inválidas');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Email:
        <input
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          aria-label="Email"
        />
      </label>
      <label>
        Senha:
        <input
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          aria-label="Senha"
        />
      </label>
      <button type="submit">Entrar</button>
    </form>
  );
}

async function fakeApiLogin(email, password) {
  // Simulação de atraso de rede
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ success: email === 'user@example.com' && password === 'password' });
    }, 500);
  });
}

export default LoginForm;
```

**Dashboard.jsx**
```jsx
import React from 'react';

function Dashboard() {
  return <h1>Bem-vindo ao Dashboard!</h1>;
}

export default Dashboard;
```

**App.jsx**
```jsx
import React, { useState } from 'react';
import LoginForm from './LoginForm';
import Dashboard from './Dashboard';

function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const handleLoginSuccess = () => {
    setIsLoggedIn(true);
  };

  return <>{isLoggedIn ? <Dashboard /> : <LoginForm onLoginSuccess={handleLoginSuccess} />}</>;
}

export default App;
```

#### Teste de Integração:

**App.test.jsx**
```jsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import App from './App';

test('fluxo de login bem-sucedido', async () => {
  render(<App />);

  // Verifica se o formulário de login é renderizado
  const emailInput = screen.getByLabelText(/email/i);
  const passwordInput = screen.getByLabelText(/senha/i);
  const submitButton = screen.getByRole('button', { name: /entrar/i });

  expect(emailInput).toBeInTheDocument();
  expect(passwordInput).toBeInTheDocument();
  expect(submitButton).toBeInTheDocument();

  // Preenche o formulário
  fireEvent.change(emailInput, { target: { value: 'user@example.com' } });
  fireEvent.change(passwordInput, { target: { value: 'password' } });
  fireEvent.click(submitButton);

  // Aguarda a atualização da interface
  await waitFor(() => {
    // Verifica se o dashboard é renderizado após o login
    const dashboardHeading = screen.getByText(/bem-vindo ao dashboard!/i);
    expect(dashboardHeading).toBeInTheDocument();
  });
});

test('fluxo de login com falha', async () => {
  render(<App />);

  // Verifica se o formulário de login é renderizado
  const emailInput = screen.getByLabelText(/email/i);
  const passwordInput = screen.getByLabelText(/senha/i);
  const submitButton = screen.getByRole('button', { name: /entrar/i });

  // Preenche o formulário com credenciais inválidas
  fireEvent.change(emailInput, { target: { value: 'invalid@example.com' } });
  fireEvent.change(passwordInput, { target: { value: 'wrongpassword' } });
  fireEvent.click(submitButton);

  // Verifica se a mensagem de erro é exibida
  await waitFor(() => {
    expect(window.alert).toHaveBeenCalledWith('Credenciais inválidas');
  });

  // Verifica que o dashboard não é renderizado
  const dashboardHeading = screen.queryByText(/bem-vindo ao dashboard!/i);
  expect(dashboardHeading).not.toBeInTheDocument();
});
```

###### **Observações:**

- Utilizamos `await waitFor()` para esperar por atualizações assíncronas na interface.
- No teste de fluxo de login com falha, simulamos uma mensagem de alerta. Para testar o `alert`, você pode mockar o `window.alert` no início do teste:
```jsx
jest.spyOn(window, 'alert').mockImplementation(() => {});
```


## Dicas e Boas Práticas

### Simulando Chamadas de API

Para testes de integração mais realistas, é comum utilizar ferramentas como o `msw` (Mock Service Worker) para simular respostas de API.

**Instalação do msw:**
```bash
npm install --save-dev msw
```

Configuração básica:
```jsx
// src/mocks/handlers.js
import { rest } from 'msw';

export const handlers = [
  rest.post('/login', (req, res, ctx) => {
    const { email, password } = req.body;
    if (email === 'user@example.com' && password === 'password') {
      return res(ctx.status(200), ctx.json({ success: true }));
    } else {
      return res(ctx.status(401), ctx.json({ success: false }));
    }
  }),
];

// src/mocks/server.js
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

Configurando o Jest:
```jsx
// src/setupTests.js
import { server } from './mocks/server';
import '@testing-library/jest-dom/extend-expect';

// Inicia o servidor antes dos testes
beforeAll(() => server.listen());

// Reseta os handlers após cada teste para evitar interferência entre testes
afterEach(() => server.resetHandlers());

// Fecha o servidor após os testes
afterAll(() => server.close());
```

Atualizando o testes:
```js
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import App from './App';

test('fluxo de login bem-sucedido com msw', async () => {
  render(<App />);

  // ... (restante do teste permanece o mesmo)

  // Agora, as chamadas à API serão interceptadas pelo msw
});
```

### 4.2. Testando Contextos e Estados Globais

Se sua aplicação utiliza o React Context ou bibliotecas de estado global como Redux, você pode envolver seu componente de teste com os provedores necessários.

**Exemplo com React Context:**
```js
// UserContext.jsx
import React, { createContext, useState } from 'react';

export const UserContext = createContext();

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);

  return (
    <UserContext.Provider value={{ user, login }}>
      {children}
    </UserContext.Provider>
  );
}
```

Envolvendo o componente no teste:
```jsx
import { UserProvider } from './UserContext';

test('teste com UserContext', () => {
  render(
    <UserProvider>
      <App />
    </UserProvider>
  );

  // ... teste continua
});
```

###### Evitar Interferência Entre Testes

- **Limpeza do DOM:** O React Testing Library limpa o DOM após cada teste, mas se você criar variáveis globais ou mocks, certifique-se de resetá-los.
- **Isolamento de Testes:** Cada teste deve ser independente. Evite dependências entre testes.


Realizar testes de integração com o React Testing Library envolve renderizar componentes em conjunto e simular interações do usuário para verificar se o comportamento da aplicação é o esperado.

**Passos-chave:**

- Renderize o componente raiz ou uma combinação de componentes que representam o fluxo a ser testado.
- Utilize os métodos de query do RTL para interagir com o DOM (por exemplo, `getBy`, `findBy`, `queryBy`).
- Simule interações do usuário com `fireEvent` ou `userEvent` (uma biblioteca que simula eventos de forma mais realista).
- Utilize `waitFor` ou `findBy` para testes assíncronos.
- Simule chamadas de API com ferramentas como `msw` para tornar seus testes mais robustos e próximos da realidade.
- Envolva seus componentes em provedores de contexto ou estado global quando necessário.

**Ferramentas Adicionais:**

- **user-event:** [User Events](User%20Events.md) Biblioteca que complementa o RTL para simular eventos do usuário de forma mais próxima do real.