---
tags:
  - Tecnologia
  - SQL
  - Postgres
---
A seguir, apresento uma explicação detalhada dos principais tipos de dados disponíveis no PostgreSQL, que permitem desde o armazenamento de informações simples até estruturas complexas e aplicações especializadas:

---

## 1. Tipos Numéricos

### Inteiros:

- **smallint:**
    
    - **Descrição:** Armazena inteiros pequenos.
    - **Armazenamento:** 2 bytes.
    - **Intervalo:** De -32.768 a 32.767.
- **integer (ou int):**
    
    - **Descrição:** O tipo inteiro mais comum, adequado para a maioria dos casos.
    - **Armazenamento:** 4 bytes.
    - **Intervalo:** De -2.147.483.648 a 2.147.483.647.
- **bigint:**
    
    - **Descrição:** Utilizado para armazenar números inteiros de grande magnitude.
    - **Armazenamento:** 8 bytes.
    - **Intervalo:** Aproximadamente de -9,22×10¹⁸ a 9,22×10¹⁸.

### Números Decimais e de Ponto Flutuante:

- **numeric (ou decimal):**
    
    - **Descrição:** Permite especificar a precisão (quantidade total de dígitos) e a escala (número de dígitos à direita da vírgula).
    - **Uso:** Ideal para aplicações financeiras e cálculos que exigem alta precisão, evitando os erros de arredondamento comuns em números de ponto flutuante.
- **real:**
    
    - **Descrição:** Número de ponto flutuante de precisão simples.
    - **Armazenamento:** 4 bytes.
    - **Uso:** Adequado quando a precisão não é crítica.
- **double precision:**
    
    - **Descrição:** Número de ponto flutuante de precisão dupla.
    - **Armazenamento:** 8 bytes.
    - **Uso:** Preferível quando se necessita de maior precisão em cálculos.

### Outros:

- **money:**
    - **Descrição:** Um tipo específico para valores monetários.
    - **Detalhes:** Formata automaticamente os números de acordo com a localidade; contudo, pode apresentar limitações em cálculos e conversões.

---

## 2. Tipos de Caracteres e Cadeias

### Armazenamento de Texto:

- **char(n):**
    
    - **Descrição:** Armazena uma cadeia de caracteres de tamanho fixo.
    - **Detalhe:** Se a string fornecida tiver menos de _n_ caracteres, espaços em branco são adicionados ao final.
- **varchar(n):**
    
    - **Descrição:** Armazena uma cadeia de caracteres de tamanho variável com limite máximo definido.
    - **Uso:** Ideal para quando se conhece o tamanho máximo esperado, economizando espaço.
- **text:**
    
    - **Descrição:** Permite armazenar textos de comprimento variável sem restrição de tamanho.
    - **Uso:** Útil para textos longos, como descrições ou conteúdos extensos, sem preocupação com limites.

---

## 3. Tipos de Data e Hora

### Manipulação de Datas e Horários:

- **date:**
    
    - **Descrição:** Armazena apenas a data (ano, mês, dia).
    - **Uso:** Para registros onde apenas a data é relevante.
- **time [with/without time zone]:**
    
    - **Descrição:** Armazena a hora do dia.
    - **Detalhe:** Pode ser com ou sem informação de fuso horário, dependendo da aplicação.
- **timestamp [with/without time zone]:**
    
    - **Descrição:** Combina data e hora.
    - **Uso:** Fundamental para registrar momentos precisos, como log de eventos.
    - **Detalhe:** A versão com fuso horário é recomendada para aplicações distribuídas globalmente.

### Intervalos:

- **interval:**
    - **Descrição:** Armazena períodos ou durações (ex.: “2 dias”, “3 horas 15 minutos”).
    - **Uso:** Muito utilizado para cálculos de tempo, como a diferença entre datas.

---

## 4. Tipos Booleanos

- **boolean:**
    - **Descrição:** Armazena valores lógicos.
    - **Valores:** `true` ou `false` (além de `null`, caso o valor não seja definido).

---

## 5. Tipos Geométricos

Esses tipos facilitam o trabalho com dados espaciais e geométricos:

- **point:** Representa um ponto no plano (coordenadas X e Y).
- **line:** Define uma linha infinita.
- **lseg (line segment):** Armazena um segmento de reta (linha limitada).
- **box:** Representa uma caixa retangular definida por dois pontos opostos.
- **path:** Pode ser uma sequência aberta ou fechada de pontos.
- **polygon:** Armazena um polígono fechado.
- **circle:** Define um círculo a partir de um ponto central e um raio.

---

## 6. Tipos de Endereço de Rede

Esses tipos são utilizados para armazenar endereços de rede e informações relacionadas:

- **inet:**
    - **Descrição:** Armazena endereços IPv4 e IPv6, podendo incluir a máscara de rede.
- **cidr:**
    - **Descrição:** Específico para representar redes; garante que os bits não significativos estejam zerados.
- **macaddr:**
    - **Descrição:** Armazena endereços MAC, comuns em interfaces de rede.

---

## 7. Tipos de Bit String

- **bit(n):**
    - **Descrição:** Armazena uma sequência fixa de bits.
- **bit varying(n):**
    - **Descrição:** Armazena uma sequência variável de bits, permitindo flexibilidade no tamanho até um máximo definido.

---

## 8. Tipos de Dados Estruturados e Documentos

### Armazenamento de Dados Semiestruturados:

- **json:**
    
    - **Descrição:** Permite armazenar dados no formato JSON mantendo a estrutura original do documento.
    - **Uso:** Ideal para aplicações que manipulam dados dinâmicos ou com estrutura variável.
- **jsonb:**
    
    - **Descrição:** Versão binária do JSON, que possibilita indexação e consultas mais eficientes.
    - **Uso:** Preferida quando a performance em consultas é crítica.
- **xml:**
    
    - **Descrição:** Armazena dados no formato XML, suportando validação e consultas com XPath.

---

## 9. Outros Tipos Avançados

### Tipos Especiais e Personalizáveis:

- **UUID:**
    
    - **Descrição:** Armazena identificadores únicos universais, garantindo unicidade global.
    - **Uso:** Comum para chaves primárias distribuídas.
- **Arrays:**
    
    - **Descrição:** Permite armazenar coleções de valores de um mesmo tipo (ex.: array de inteiros, textos, etc.).
    - **Uso:** Útil para quando se deseja manter listas de valores dentro de uma mesma coluna.
- **Range Types:**
    
    - **Descrição:** Armazena intervalos de valores, como _int4range_ (intervalo de inteiros) ou _tsrange_ (intervalo de timestamps).
    - **Uso:** Útil para representar períodos, intervalos numéricos ou de tempo, facilitando operações como sobreposição e verificação de intervalos.
- **hstore:**
    
    - **Descrição:** Um tipo para armazenar pares chave-valor, proporcionando uma forma simples de manipular dados semiestruturados.
    - **Uso:** Pode ser uma alternativa leve ao JSON quando a estrutura dos dados é relativamente simples.
- **Tipos Compostos e Personalizados:**
    
    - **Descrição:** O PostgreSQL permite a criação de tipos de dados definidos pelo usuário, atendendo a necessidades específicas que não são cobertas pelos tipos padrão.

---

## Considerações Finais

A variedade de tipos de dados no PostgreSQL proporciona uma flexibilidade extraordinária para modelar qualquer cenário, desde aplicações simples que utilizam textos e números até sistemas complexos que necessitam de dados geográficos, estruturas semiestruturadas ou intervalos de valores. A escolha do tipo adequado pode influenciar diretamente na performance, integridade e facilidade de manutenção da base de dados. Por isso, é importante avaliar as necessidades específicas de sua aplicação ao definir os tipos de dados a serem utilizados.

Essa riqueza de tipos torna o PostgreSQL uma opção robusta e versátil para uma ampla gama de aplicações, desde sistemas de gestão simples até soluções de alta complexidade.

---