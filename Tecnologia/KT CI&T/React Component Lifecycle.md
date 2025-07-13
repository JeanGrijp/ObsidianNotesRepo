- Introdução
- class component vs function component
- Principais Métodos do Ciclo de Vida em Componentes de Classe
- Hooks e o Ciclo de Vida em Componentes Funcionais
- Quando Usar Cada Parte do Ciclo de Vida?

# # Introdução ao Ciclo de Vida do React

No contexto do React, **ciclo de vida** se refere ao processo completo de “vida” de um componente, desde o momento em que ele é criado (montado) na árvore de componentes, passando por possíveis atualizações, até o momento em que é removido (desmontado). Assim como organismos vivos, um componente React nasce, se desenvolve, pode sofrer mudanças ao longo do tempo, e eventualmente é eliminado.

### Por que entender o ciclo de vida é importante?

Compreender o ciclo de vida permite que o desenvolvedor saiba exatamente quando executar determinados tipos de código, como:

- Buscar dados em uma API quando o componente for exibido pela primeira vez;
- Configurar e limpar timers, listeners ou assinaturas de eventos;
- Otimizar re-renderizações desnecessárias, melhorando performance;
- Prevenir vazamentos de memória ao remover recursos que não são mais necessários;
- Integrar bibliotecas de terceiros e manipular elementos do DOM de forma controlada.

Sem esse entendimento, é comum encontrar bugs relacionados a dados desatualizados, múltiplos listeners duplicados, ou até mesmo travamentos inesperados na aplicação.

# Fases do Ciclo de Vida do React

O ciclo de vida de um componente React pode ser dividido em três fases principais:

1. **Montagem (Mounting)**
2. **Atualização (Updating)**
3. **Desmontagem (Unmounting)**

Cada uma dessas fases representa um momento importante na “vida” do componente e permite que você execute ações específicas de acordo com a necessidade da sua aplicação.
## 1. Montagem (Mounting)

A montagem ocorre quando o componente é criado e inserido na árvore do DOM pela primeira vez. Neste momento, você pode inicializar estados, buscar dados e realizar outras configurações iniciais.

- **Componentes de Classe:**
  - `constructor`
  - `componentDidMount`
  - (opcional) `getDerivedStateFromProps`

- **Componentes Funcionais:**
  - `useEffect(() => { ... }, [])`  *(executa uma vez ao montar)*

### Exemplo de Montagem

#### Componente de Classe

```tsx
class ExemploClasse extends React.Component {
  constructor(props) {
    super(props);
    this.state = { data: null };
  }

  componentDidMount() {
    // Busca dados ao montar
    fetch('/api/data')
      .then(res => res.json())
      .then(data => this.setState({ data }));
  }

  render() {
    return <div>{this.state.data}</div>;
  }
}
```

#### Componente funcional
```tsx
import { useEffect, useState } from 'react';

function ExemploFuncional() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then(res => res.json())
      .then(setData);
  }, []);

  return <div>{data}</div>;
}
```


## 2. Atualização (Updating)

A fase de atualização acontece sempre que há uma mudança de `state` ou `props`, levando o componente a re-renderizar. Nessa fase, é possível responder a mudanças e otimizar as atualizações.

- **Componentes de Classe:**
    
    - `shouldComponentUpdate`
        
    - `componentDidUpdate`
        
- **Componentes Funcionais:**
    
    - `useEffect(() => { ... }, [dependências])` _(executa quando dependências mudam)_
        

## Exemplo de Atualização

#### Componente de Classe

```tsx
class ExemploClasse extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    if (prevProps.id !== this.props.id) {
      // Buscar novos dados se a prop id mudou
      fetch(`/api/data/${this.props.id}`)
        .then(res => res.json())
        .then(data => this.setState({ data }));
    }
  }
}
```

#### Componente funcional

```tsx
import { useEffect, useState } from 'react';

function ExemploFuncional({ id }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch(`/api/data/${id}`)
      .then(res => res.json())
      .then(setData);
  }, [id]);

  return <div>{data}</div>;
}
```

## 3. Desmontagem (Unmounting)

A desmontagem ocorre quando o componente é removido da interface. Este é o momento de limpar recursos, como timers, assinaturas de eventos e conexões.

- **Componentes de Classe:**
    
    - `componentWillUnmount`
        
- **Componentes Funcionais:**
    
    - Função de limpeza (cleanup) dentro do `useEffect`
        

### Exemplo de Desmontagem

#### Componente de Classe

```tsx
class ExemploClasse extends React.Component {
  componentDidMount() {
    this.timer = setInterval(() => {
      // alguma ação
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timer);
  }
}
```

#### Componente funcional

```tsx
import { useEffect } from 'react';

function ExemploFuncional() {
  useEffect(() => {
    const timer = setInterval(() => {
      // alguma ação
    }, 1000);

    return () => {
      clearInterval(timer); // Limpeza ao desmontar
    };
  }, []);

  return <div>Componente funcional</div>;
}

```


## Desmontagem e Limpeza em Componentes Funcionais com useEffect

Quando um componente React é removido da interface, ele passa pela fase de **desmontagem** (*unmounting*). Em componentes funcionais, a desmontagem é tratada usando uma **função de limpeza** (cleanup) retornada dentro do `useEffect`. Esse mecanismo é essencial para evitar problemas como vazamentos de memória, execuções de código desnecessárias e conflitos entre múltiplas instâncias do mesmo componente.

### Por que a função de limpeza é importante?

Ao criar efeitos colaterais dentro de um componente — como timers, subscriptions, listeners de eventos ou conexões — é fundamental que esses recursos sejam removidos assim que o componente deixa de existir. Caso contrário, eles podem continuar ativos em segundo plano, ocupando memória ou executando ações que não fazem mais sentido, já que o componente não está mais visível para o usuário.

Sem a função de limpeza, você pode enfrentar problemas como:

- Vazamento de memória (memory leak)
- Execução de efeitos em componentes já desmontados (causando erros no console)
- Listeners duplicados, causando comportamentos inesperados

### Como funciona a função de limpeza no useEffect?

O `useEffect` pode retornar uma função. Essa função é chamada **automaticamente** pelo React quando:

1. O componente será desmontado da tela.
2. O efeito vai ser executado novamente, porque alguma dependência mudou.

**Sintaxe básica:**

```jsx
useEffect(() => {
  // Código do efeito (executa ao montar e/ou atualizar)

  return () => {
    // Código de limpeza (executa ao desmontar ou antes de reexecutar o efeito)
  };
}, [dependências]);
```

- Se o array de dependências estiver vazio (`[]`), a limpeza será chamada apenas ao desmontar o componente.
    
- Se houver dependências, a limpeza será chamada antes do efeito ser reexecutado (quando as dependências mudam) **e** ao desmontar o componente.
    

### Exemplos práticos de limpeza com useEffect

###### 1. Limpando um timer
```tsx
import { useEffect } from 'react';

function TimerComponent() {
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Rodando...');
    }, 1000);

    // Limpeza: cancela o timer ao desmontar
    return () => {
      clearInterval(timer);
      console.log('Timer cancelado');
    };
  }, []);

  return <div>Timer ativo</div>;
}
```

###### 2. Removendo um event listener
```jsx
import { useEffect } from 'react';

function ResizeListener() {
  useEffect(() => {
    const handleResize = () => {
      console.log('Window foi redimensionada');
    };

    window.addEventListener('resize', handleResize);

    // Limpeza: remove o event listener ao desmontar
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return <div>Observando o resize</div>;
}
```


#### 3. Cancelando uma requisição assíncrona

Em requisições que podem demorar, é possível usar um sinal de abort para evitar tentar atualizar o estado de um componente que já foi desmontado.
```jsx
import { useEffect, useState } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch('https://jsonplaceholder.typicode.com/posts', {
      signal: controller.signal,
    })
      .then((res) => res.json())
      .then(setData)
      .catch((err) => {
        if (err.name !== 'AbortError') {
          console.error(err);
        }
      });

    // Limpeza: cancela a requisição caso o componente desmonte
    return () => {
      controller.abort();
    };
  }, []);

  return <div>{data ? 'Dados carregados' : 'Carregando...'}</div>;
}

```

## Exemplos de Mal Uso do useEffect Sem Limpeza

##### Vazamento de Memória (Memory Leak)

Imagine um componente que usa um timer (`setInterval`) para atualizar o tempo na tela, mas **não limpa o timer** ao ser desmontado.

```jsx
import { useEffect, useState } from 'react';

function RelogioSemLimpeza() {
  const [hora, setHora] = useState(new Date());

  useEffect(() => {
    const timer = setInterval(() => {
      setHora(new Date());
    }, 1000);

    // Não há função de limpeza aqui!
  }, []);

  return <div>{hora.toLocaleTimeString()}</div>;
}
```

**Problema:**  
Ao navegar para fora desse componente, o timer continua rodando em segundo plano, tentando atualizar o estado de um componente que já foi removido. Isso consome memória desnecessariamente e pode travar a aplicação se ocorrer muitas vezes.


# Quando Usar Cada Parte do Ciclo de Vida do React?

Saber **quando** utilizar cada etapa do ciclo de vida permite que você escreva componentes mais eficientes, organizados e livres de bugs. Cada fase tem uma finalidade específica e é mais apropriada para determinados tipos de lógica. Veja abaixo as situações mais comuns:

## 1. Buscar Dados de APIs e Recursos Externos

**Quando usar:**  
Quando o componente for exibido na tela pela primeira vez.

- **Componente de Classe:** `componentDidMount`
- **Componente Funcional:** `useEffect(() => { ... }, [])`

**Por quê?**  
Esse momento garante que a árvore do DOM já foi construída, então é seguro fazer requisições, manipular o DOM ou integrar bibliotecas externas.

## 2. Limpar Recursos, Listeners e Timers

**Quando usar:**  
Quando o componente for removido da tela (desmontado) ou antes do efeito ser reexecutado devido à mudança de dependências.

- **Componente de Classe:** `componentWillUnmount`
- **Componente Funcional:** função de limpeza no `useEffect`

**Por quê?**  
Assim você evita vazamento de memória, listeners duplicados e timers rodando sem necessidade.

## 3. Reagir a Mudanças de Props ou Estado

**Quando usar:**  
Sempre que uma prop ou estado específico mudar e você precisar executar alguma ação (buscar novos dados, sincronizar com recursos externos, etc).

- **Componente de Classe:** `componentDidUpdate` (com checagem das props/estado anteriores)
- **Componente Funcional:** `useEffect(() => { ... }, [dependências])` (coloque as variáveis relevantes no array de dependências)

**Por quê?**  
Permite que seu componente responda dinamicamente a mudanças, sem executar lógicas desnecessárias.

## 4. Inicialização de Estado e Ligação de Métodos

**Quando usar:**  
Logo no início da criação do componente.

- **Componente de Classe:** `constructor`
- **Componente Funcional:** inicialização direta com `useState`, `useReducer` ou declaração de funções inline.

**Por quê?**  
Aqui é o local certo para definir valores iniciais do estado ou criar funções/métodos que serão usados no componente.

## Resumo Visual

| Situação                             | Classe                 | Funcional (Hooks)                |
| ------------------------------------ | ---------------------- | -------------------------------- |
| Buscar dados ao montar               | `componentDidMount`    | `useEffect(..., [])`             |
| Limpar timers/listeners ao desmontar | `componentWillUnmount` | `useEffect` (com return)         |
| Reagir a mudanças de props/estado    | `componentDidUpdate`   | `useEffect(..., [dependências])` |
| Inicializar estado/métodos           | `constructor`          | `useState`, função inline        |
|                                      |                        |                                  |

#Beba_agua 
