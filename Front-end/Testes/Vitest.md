---
tags:
  - Tecnologia
  - testes
---
# Como criar testes unitários no front-end com react.js e Vitest

Criar testes unitários no front-end usando [React](React.md) pode ser feito com várias ferramentas, mas uma das mais populares é o `Jest`, frequentemente combinado com a biblioteca `React Testing Library`. Aqui está um guia básico para começar:
### Passo 1: Instalação das Dependências

Primeiro, instale o `Jest` e o `React Testing Library` no seu projeto:

```shell
yarn add --dev jest @testing-library/react @testing-library/jest-dom
```


## Configurando o Vitest[​](https://vitest.dev/guide/#configuring-vitest)

Uma das principais vantagens do Vitest é sua configuração unificada com o Vite. Se presente, `vitest`lerá sua raiz `vite.config.ts`para corresponder aos plugins e configurar como seu aplicativo Vite. Por exemplo, sua configuração [resolve.alias](https://vitejs.dev/config/shared-options.html#resolve-alias) e [plugins](https://vitejs.dev/guide/using-plugins.html) do Vite funcionará imediatamente. Se você quiser uma configuração diferente durante o teste, você pode:

- Crie `vitest.config.ts`, que terá a prioridade mais alta
- Passe `--config`a opção para CLI, por exemplo`vitest --config ./path/to/vitest.config.ts`
- Use `process.env.VITEST`ou `mode`propriedade em `defineConfig`(será definido como `test`se não for substituído) para aplicar condicionalmente uma configuração diferente em`vite.config.ts`

O Vitest suporta as mesmas extensões para seu arquivo de configuração que o Vite: `.js`, `.mjs`, `.cjs`, `.ts`, `.cts`, `.mts`. O Vitest não suporta `.json`extensão.

Se você não estiver usando o Vite como sua ferramenta de construção, você pode configurar o Vitest usando a `test`propriedade no seu arquivo de configuração:

```tsx
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // ...
  },
})
```

###### DICA

Mesmo que você não use o Vite, o Vitest depende muito dele para seu pipeline de transformação. Por esse motivo, você também pode configurar qualquer propriedade descrita na [documentação do Vite](https://vitejs.dev/config/) .

Se você já estiver usando o Vite, adicione `test`a propriedade na sua configuração do Vite. Você também precisará adicionar uma referência aos tipos do Vitest usando uma [diretiva de barra tripla](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html#-reference-types-) no topo do seu arquivo de configuração.

```tsx
/// <reference types="vitest" />
import { defineConfig } from 'vite'

export default defineConfig({
  test: {
    // ...
  },
})
```