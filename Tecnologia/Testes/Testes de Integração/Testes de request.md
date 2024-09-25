---
tags:
  - Tecnologia
  - testes
---
### **Como Testar um Hook Personalizado que Faz Requisições HTTP**

**Como testar o hook personalizado `useMovieService` que faz requisições HTTP**

Fala pessoal, tudo bem? Vou mostrar pra vocês como podemos testar um hook personalizado no React que faz requisições HTTP. No nosso caso, vamos focar no hook `useMovieService`, que é responsável por várias funções relacionadas a filmes, como obter detalhes, imagens, reviews, entre outros.

```tsx
import axios from "axios";
import {
  IMovieCreditsCast,
  IMovieDetails,
  IMoviePhoto,
  IMovieReview,
  IMoviesIds,
  IPopularMovies,
  IQuerySearchMovie,
} from "../../@types/Movie";
import { BaseMovieUrl, BaseUrlSearch } from "../baseUrl/APIBaseUrl";
import { useContext } from "react";
import { FiltersContext } from "../../context/filters";
import { ITVData } from "../../@types/TVSerie";

interface IUseMovieFunctions {
  getMoviesList: () => Promise<IMoviesIds>;
  getMovieDetails: (
    movieId: number,
    language?: string
  ) => Promise<IMovieDetails>;
  getImageMovie: (moviePath: string) => Promise<Blob>;
  getPopularMovies: ({
    language,
    page,
    region,
  }: {
    language?: string;
    page?: number;
    region?: string;
  }) => Promise<IPopularMovies>;
  getSearchMovies: (query: IQuerySearchMovie) => Promise<IPopularMovies>;
  getMoviePhotos: (movieId: number) => Promise<IMoviePhoto>;
  getMovieCreditCast: (movieId: number) => Promise<IMovieCreditsCast>;
  getMovieSimilar: (movieId: number) => Promise<IPopularMovies>;
  getMoviesRecommendations: (movieId: number) => Promise<IPopularMovies>;
  getMoviesWatchProviders: (movieId: number) => Promise<ITVData>;
  getMovieReviews: (movieId: number) => Promise<IMovieReview>;
}

const useMovieService = (): IUseMovieFunctions => {
  const { idiomaSelected } = useContext(FiltersContext);

  const getPopularMovies = async ({
    language = idiomaSelected,
    page,
    region,
  }: {
    language?: string;
    page?: number;
    region?: string;
  }): Promise<IPopularMovies> => {
    const response = await BaseMovieUrl.get("/popular", {
      params: {
        language,
        page,
        region,
      },
    });
    return response.data;
  };

  const getMoviesList = async (): Promise<IMoviesIds> => {
    const response = await BaseMovieUrl.get("/changes");
    return response.data;
  };

  const getMovieSimilar = async (movieId: number): Promise<IPopularMovies> => {
    const response = await BaseMovieUrl.get(`/${movieId}/similar`, {
      params: {
        language: idiomaSelected,
      },
    });
    return response.data;
  };

  const getMoviesRecommendations = async (
    movieId: number
  ): Promise<IPopularMovies> => {
    const response = await BaseMovieUrl.get(`/${movieId}/recommendations`, {
      params: {
        language: idiomaSelected,
      },
    });
    return response.data;
  };

  const getMovieDetails = async (movieId: number): Promise<IMovieDetails> => {
    const response = await BaseMovieUrl.get(`/${movieId}`, {
      params: {
        language: idiomaSelected,
      },
    });
    return response.data;
  };

  const getImageMovie = async (moviePath: string): Promise<Blob> => {
    const response = await axios.get(
      `https://image.tmdb.org/t/p/original/${moviePath}`
    );
    return response.data;
  };

  const getSearchMovies = async (
    query: IQuerySearchMovie
  ): Promise<IPopularMovies> => {
    const response = await BaseUrlSearch.get("/movie", {
      params: query,
    });
    return response.data;
  };

  const getMoviePhotos = async (movieId: number): Promise<IMoviePhoto> => {
    const response = await BaseMovieUrl.get(`/${movieId}/images`);
    return response.data;
  };

  const getMovieCreditCast = async (
    movieId: number
  ): Promise<IMovieCreditsCast> => {
    const response = await BaseMovieUrl.get(`/${movieId}/credits`);
    return response.data;
  };

  const getMoviesWatchProviders = async (movieId: number): Promise<ITVData> => {
    const response = await BaseMovieUrl.get(`/${movieId}/watch/providers`, {
      params: {
        language: idiomaSelected,
      },
    });
    return response.data;
  };

  const getMovieReviews = async (movieId: number): Promise<IMovieReview> => {
    const response = await BaseMovieUrl.get(`/${movieId}/reviews`, {
      params: {
        language: idiomaSelected,
      },
    });
    return response.data;
  };

  return {
    getMoviesList,
    getMovieDetails,
    getImageMovie,
    getPopularMovies,
    getSearchMovies,
    getMoviePhotos,
    getMovieCreditCast,
    getMovieSimilar,
    getMoviesRecommendations,
    getMoviesWatchProviders,
    getMovieReviews,
  };
};

export default useMovieService;
```

Vou explicar passo a passo como configurar o ambiente de teste, mockar as dependências e escrever os testes para cada função do hook.

### **1. Entendendo o hook e suas dependências**

O `useMovieService` depende de duas coisas principais:

- **Axios**: Biblioteca usada para fazer as requisições HTTP.
- **`FiltersContext`**: Contexto de onde pegamos o idioma selecionado (`idiomaSelected`).

As funções exportadas pelo hook realizam operações como:

- Obter a lista de filmes populares.
- Buscar detalhes de um filme específico.
- Obter imagens, elenco, filmes similares, recomendações, provedores de exibição e reviews.

### **2. Configurando o ambiente de teste**

Primeiro, precisamos garantir que temos todas as dependências necessárias instaladas:

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom axios
```

- **jest**: Framework de testes.
- **@testing-library/react**: Para renderizar componentes React nos testes.
- **@testing-library/jest-dom**: Fornece matchers adicionais para o Jest.
- **axios**: Biblioteca para fazer requisições HTTP.

### **3. Mockando as dependências externas**

Para testar o nosso hook sem depender de serviços externos ou do contexto real, precisamos mockar o Axios e o `FiltersContext`.

#### **3.1. Mockando o Axios**

Criamos um mock do Axios para evitar que requisições reais sejam feitas durante os testes.

- Crie um arquivo chamado `__mocks__/axios.js` na raiz do projeto ou na pasta de mocks:
```js
// __mocks__/axios.js
export default {
  get: jest.fn(),
  post: jest.fn(),
  put: jest.fn(),
  delete: jest.fn(),
};
```

- No arquivo de teste, informamos ao Jest para usar o mock:
```jsx
jest.mock('axios');
```

#### **3.2. Mockando o `FiltersContext`**

Como o hook depende do `FiltersContext` para obter o idioma selecionado, precisamos mockar esse contexto nos testes.

- Podemos criar um mock simples do `FiltersContext`:
```jsx
// __mocks__/filters.js
import React from 'react';

export const FiltersContext = React.createContext();

export const FiltersProvider = ({ children }) => {
  return (
    <FiltersContext.Provider value={{ idiomaSelected: 'pt-BR' }}>
      {children}
    </FiltersContext.Provider>
  );
};
```

- No nosso teste, importamos o `FiltersProvider` do mock:

```jsx
jest.mock('../../context/filters', () => ({
  FiltersContext: require('react').createContext(),
}));
```

### **4. Escrevendo os testes para o hook**

Como os hooks precisam ser usados dentro de um componente funcional, vamos criar um componente de teste que utiliza o `useMovieService`. Isso nos permitirá testar as funções expostas pelo hook.

#### **4.1. Criando um componente de teste**

```jsx
// TestComponent.jsx
import React, { useEffect } from 'react';
import useMovieService from './useMovieService';

function TestComponent({ onHookReady }) {
  const movieService = useMovieService();

  useEffect(() => {
    if (onHookReady) {
      onHookReady(movieService);
    }
  }, [movieService, onHookReady]);

  return null;
}

export default TestComponent;
```

#### **4.2. Utilizando o `FiltersProvider` no teste**

Envolvemos o `TestComponent` com o `FiltersProvider` para fornecer o contexto necessário durante os testes.

### **5. Exemplos de testes para cada função**

Agora, vamos escrever testes para cada função que o nosso hook fornece.

**5.1. Importações necessárias**
```jsx
// useMovieService.test.js
import React from 'react';
import { render } from '@testing-library/react';
import axios from 'axios';
import TestComponent from './TestComponent';
import { FiltersProvider } from '../../context/filters';

jest.mock('axios');
jest.mock('../../context/filters', () => ({
  FiltersContext: require('react').createContext(),
}));
```

**5.2. Limpando os mocks entre os testes**

Para evitar que um teste interfira em outro, limpamos os mocks após cada teste:
```jsx
afterEach(() => {
  jest.clearAllMocks();
});
```

5.3. Testando `getPopularMovies`
```jsx
test('deve retornar filmes populares', async () => {
  const mockData = { results: [{ id: 1, title: 'Filme Popular' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const popularMovies = await movieService.getPopularMovies({});

  expect(axios.get).toHaveBeenCalledWith('/popular', {
    params: {
      language: 'pt-BR',
      page: undefined,
      region: undefined,
    },
  });

  expect(popularMovies).toEqual(mockData);
});
```

5.4. Testando `getMovieDetails`
```jsx
test('deve retornar detalhes do filme', async () => {
  const movieId = 123;
  const mockData = { id: movieId, title: 'Detalhes do Filme' };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const movieDetails = await movieService.getMovieDetails(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}`, {
    params: {
      language: 'pt-BR',
    },
  });

  expect(movieDetails).toEqual(mockData);
});
```

5.5. Testando `getImageMovie`
```jsx
test('deve retornar a imagem do filme', async () => {
  const moviePath = 'path/to/image.jpg';
  const mockBlob = new Blob(['image data'], { type: 'image/jpeg' });

  axios.get.mockResolvedValue({ data: mockBlob });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const image = await movieService.getImageMovie(moviePath);

  expect(axios.get).toHaveBeenCalledWith(
    `https://image.tmdb.org/t/p/original/${moviePath}`
  );

  expect(image).toEqual(mockBlob);
});
```

5.6. Testando `getSearchMovies`
```jsx
test('deve retornar filmes com base na busca', async () => {
  const query = { query: 'Ação' };
  const mockData = { results: [{ id: 1, title: 'Filme de Ação' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const searchResults = await movieService.getSearchMovies(query);

  expect(axios.get).toHaveBeenCalledWith('/movie', {
    params: query,
  });

  expect(searchResults).toEqual(mockData);
});
```

5.7. Testando `getMoviePhotos`
```jsx
test('deve retornar fotos do filme', async () => {
  const movieId = 123;
  const mockData = { backdrops: [{ file_path: '/path.jpg' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const photos = await movieService.getMoviePhotos(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/images`);

  expect(photos).toEqual(mockData);
});
```

5.8. Testando `getMovieCreditCast`
```jsx
test('deve retornar elenco do filme', async () => {
  const movieId = 123;
  const mockData = { cast: [{ id: 1, name: 'Ator' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const credits = await movieService.getMovieCreditCast(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/credits`);

  expect(credits).toEqual(mockData);
});
```

5.9. Testando `getMovieSimilar`
```jsx
test('deve retornar filmes similares', async () => {
  const movieId = 123;
  const mockData = { results: [{ id: 2, title: 'Filme Similar' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const similarMovies = await movieService.getMovieSimilar(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/similar`, {
    params: {
      language: 'pt-BR',
    },
  });

  expect(similarMovies).toEqual(mockData);
});
```

5.10. Testando `getMoviesRecommendations`
```jsx
test('deve retornar recomendações de filmes', async () => {
  const movieId = 123;
  const mockData = { results: [{ id: 3, title: 'Filme Recomendado' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const recommendations = await movieService.getMoviesRecommendations(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/recommendations`, {
    params: {
      language: 'pt-BR',
    },
  });

  expect(recommendations).toEqual(mockData);
});
```

5.11. Testando `getMoviesWatchProviders`
```jsx
test('deve retornar provedores de exibição do filme', async () => {
  const movieId = 123;
  const mockData = {
    results: { BR: { flatrate: [{ provider_name: 'Netflix' }] } },
  };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const watchProviders = await movieService.getMoviesWatchProviders(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/watch/providers`, {
    params: {
      language: 'pt-BR',
    },
  });

  expect(watchProviders).toEqual(mockData);
});
```

5.12. Testando `getMovieReviews`
```jsx
test('deve retornar reviews do filme', async () => {
  const movieId = 123;
  const mockData = { results: [{ id: '1', content: 'Ótimo filme!' }] };

  axios.get.mockResolvedValue({ data: mockData });

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  const reviews = await movieService.getMovieReviews(movieId);

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}/reviews`, {
    params: {
      language: 'pt-BR',
    },
  });

  expect(reviews).toEqual(mockData);
});
```

5.13. Testando tratamento de erros
```jsx
test('deve lidar com erro ao obter detalhes do filme', async () => {
  const movieId = 123;
  const errorMessage = 'Erro na API';

  axios.get.mockRejectedValue(new Error(errorMessage));

  let movieService;

  render(
    <FiltersProvider>
      <TestComponent onHookReady={(service) => (movieService = service)} />
    </FiltersProvider>
  );

  await expect(movieService.getMovieDetails(movieId)).rejects.toThrow(
    errorMessage
  );

  expect(axios.get).toHaveBeenCalledWith(`/${movieId}`, {
    params: {
      language: 'pt-BR',
    },
  });
});
```

### Considerações finais

Testar um hook personalizado que faz requisições HTTP envolve:

- **Mockar as dependências externas**: Como o Axios e o contexto.
- **Escrever testes para cada função**: Garantindo que elas retornam os dados esperados.
- **Testar cenários de erro**: Verificando se o hook lida corretamente com falhas nas requisições.

**Dicas:**

- **Isolar os testes**: Utilize `jest.clearAllMocks()` para limpar os mocks entre os testes.
- **Manter o código organizado**: Evite repetição de código e mantenha os testes legíveis.
- **Testar diferentes cenários**: Inclua testes para casos de sucesso e de erro.