---
tags:
  - Tecnologia
  - SQL
---
Os processos de **normalização de banco de dados** são técnicas usadas para organizar os dados em um banco de dados relacional de maneira eficiente e para reduzir a redundância (duplicação de dados). O objetivo da normalização é garantir que o banco de dados seja estruturado de forma lógica e evite inconsistências. Isso melhora o desempenho e facilita a manutenção dos dados.

A normalização é dividida em **formas normais** (ou "Normal Forms" em inglês), cada uma com um conjunto de regras que o banco de dados deve seguir para estar devidamente normalizado. Vou te explicar as principais formas normais:

### 1ª Forma Normal (1NF)
- **Requisitos**:
  - Todos os valores de uma coluna (atributo) devem ser atômicos, ou seja, indivisíveis (não pode haver listas ou conjuntos de valores em uma célula).
  - Cada linha na tabela deve ser única, identificada por uma **chave primária**.
  
- **Exemplo de problema**:
  Imagine uma tabela com uma coluna "Telefones", onde você armazena vários números separados por vírgula. Isso quebra a 1ª Forma Normal.

- **Como corrigir**:
  Criar uma nova tabela onde cada telefone é armazenado em uma linha separada, com uma chave estrangeira referenciando o proprietário.

### 2ª Forma Normal (2NF)
- **Requisitos**:
  - A tabela já deve estar na 1ª Forma Normal.
  - Todos os atributos não-chave devem depender **totalmente** da chave primária. Ou seja, não pode haver dependência parcial de parte da chave primária (se a chave for composta).
  
- **Exemplo de problema**:
  Suponha uma tabela de pedidos com uma chave composta (ID do pedido e ID do produto). Se a tabela também armazenar o nome do cliente, este dado depende apenas do ID do pedido (parte da chave), e não do ID do produto.

- **Como corrigir**:
  Separar os dados em diferentes tabelas. Uma tabela para os dados do pedido (ID do pedido e cliente) e outra para os detalhes do pedido (ID do pedido, ID do produto e quantidade).

### 3ª Forma Normal (3NF)
- **Requisitos**:
  - A tabela já deve estar na 2ª Forma Normal.
  - Todos os atributos não-chave devem depender **diretamente** da chave primária e não de outros atributos não-chave (ausência de dependência transitiva).

- **Exemplo de problema**:
  Em uma tabela de funcionários, você armazena o ID do funcionário, o nome do funcionário e o departamento em que ele trabalha, e também o nome do chefe desse departamento. O nome do chefe depende do departamento, não do ID do funcionário, então há uma dependência transitiva.

- **Como corrigir**:
  Separar o nome do chefe em uma tabela de "departamentos", onde cada departamento tem seu respectivo chefe, e usar uma chave estrangeira para referenciar o departamento na tabela de funcionários.

### Forma Normal de Boyce-Codd (BCNF)
- **Requisitos**:
  - Uma versão mais rigorosa da 3ª Forma Normal. Ela lida com algumas anomalias de dependência em tabelas que ainda podem ocorrer na 3NF.
  - Para cada dependência funcional não trivial, o determinante deve ser uma chave candidata.
  
- **Exemplo de problema**:
  Imagine uma tabela de funcionários e projetos onde o funcionário pode ser o gerente de um projeto. Se houver dependência entre o funcionário e o projeto, mesmo que ambos façam parte da chave, isso pode causar problemas de redundância.

- **Como corrigir**:
  Redefinir as tabelas para garantir que cada atributo dependa totalmente de uma chave única e adequada.

### 4ª Forma Normal (4NF)
- **Requisitos**:
  - A tabela deve estar na BCNF.
  - Elimina dependências multivaloradas, ou seja, a situação em que um atributo pode depender de múltiplos valores independentes.
  
- **Exemplo de problema**:
  Se uma tabela armazena dados de funcionários com múltiplas habilidades e múltiplas localizações, há duas dependências multivaloradas (habilidade e localização). Isso pode gerar redundâncias.

- **Como corrigir**:
  Separar as dependências em diferentes tabelas: uma tabela para funcionários e habilidades, e outra para funcionários e localizações.

### 5ª Forma Normal (5NF)
- **Requisitos**:
  - A tabela deve estar na 4ª Forma Normal.
  - A 5ª forma normal trata da decomposição de dados sem perda de informações, garantindo que a reconstrução das tabelas não cause inconsistências.

---

### Por que normalizar?
1. **Redução de Redundância**: A normalização evita que os mesmos dados sejam repetidos em várias partes do banco de dados.
2. **Manutenção de Consistência**: Quando os dados são armazenados em um único lugar, as atualizações são mais fáceis e menos propensas a erros.
3. **Eficiência de Armazenamento**: A normalização também pode reduzir o espaço de armazenamento necessário ao evitar duplicação.
4. **Melhor Performance em Atualizações**: Menos duplicação significa que menos dados precisam ser modificados durante operações de atualização.

Porém, a normalização também pode tornar algumas consultas mais lentas, já que os dados podem ficar distribuídos em várias tabelas, exigindo uso de `JOIN` ([SQL](SQL.md)). Em alguns casos, desnormalizar (manter um pouco de redundância) pode ser vantajoso para otimizar a leitura.

Se precisar de mais detalhes sobre uma dessas formas normais ou um exemplo prático, só avisar!