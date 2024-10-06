---
tags:
  - Tecnologia
  - SQL
---
Os principais comandos do SQL para você começar. SQL é uma linguagem usada para gerenciar e manipular bancos de dados relacionais. Aqui estão os comandos básicos:

### 1. **SELECT**
   Usado para selecionar dados de uma tabela.
   ```sql
   SELECT column_name FROM table_name;
   ```
   Exemplo: 
   ```sql
   SELECT nome, idade FROM pessoas;
   ```

### 2. **INSERT INTO**
   Usado para inserir novos registros em uma tabela.
   ```sql
   INSERT INTO table_name (column1, column2) VALUES (value1, value2);
   ```
   Exemplo: 
   ```sql
   INSERT INTO pessoas (nome, idade) VALUES ('Jean', 30);
   ```

### 3. **UPDATE**
   Usado para atualizar dados existentes em uma tabela.
   ```sql
   UPDATE table_name SET column1 = value1 WHERE condition;
   ```
   Exemplo:
   ```sql
   UPDATE pessoas SET idade = 31 WHERE nome = 'Jean';
   ```

### 4. **DELETE**
   Usado para deletar dados de uma tabela.
   ```sql
   DELETE FROM table_name WHERE condition;
   ```
   Exemplo:
   ```sql
   DELETE FROM pessoas WHERE nome = 'Jean';
   ```

### 5. **CREATE TABLE**
   Usado para criar uma nova tabela no banco de dados.
   ```sql
   CREATE TABLE table_name (
       column1 datatype,
       column2 datatype,
       ...
   );
   ```
   Exemplo:
   ```sql
   CREATE TABLE pessoas (
       id INT PRIMARY KEY,
       nome VARCHAR(100),
       idade INT
   );
   ```

### 6. **ALTER TABLE**
   Usado para modificar uma tabela existente (adicionar ou remover colunas, por exemplo).
   ```sql
   ALTER TABLE table_name ADD column_name datatype;
   ```
   Exemplo:
   ```sql
   ALTER TABLE pessoas ADD email VARCHAR(100);
   ```

### 7. **DROP TABLE**
   Usado para deletar uma tabela inteira do banco de dados.
   ```sql
   DROP TABLE table_name;
   ```

### 8. **WHERE**
   Usado em comandos SELECT, UPDATE ou DELETE para filtrar registros com base em uma condição.
   ```sql
   SELECT * FROM pessoas WHERE idade > 30;
   ```

### 9. **JOIN**
   Usado para combinar registros de duas ou mais tabelas.
   ```sql
   SELECT column_name(s) FROM table1 
   JOIN table2 ON table1.column_name = table2.column_name;
   ```
   Exemplo:
   ```sql
   SELECT p.nome, e.email FROM pessoas p
   JOIN emails e ON p.id = e.pessoa_id;
   ```

### 10. **ORDER BY**
   Usado para ordenar o resultado de uma consulta.
   ```sql
   SELECT * FROM pessoas ORDER BY idade DESC;
   ```

Sim! Existem muitos outros comandos importantes em SQL. Vou te mostrar mais alguns comandos avançados e úteis que você pode precisar conforme progride no aprendizado:

### 11. **GROUP BY**
   Usado em conjunto com funções agregadas (como `COUNT`, `SUM`, `AVG`) para agrupar os resultados por uma ou mais colunas.
   ```sql
   SELECT column_name, COUNT(*) FROM table_name
   GROUP BY column_name;
   ```
   Exemplo:
   ```sql
   SELECT idade, COUNT(*) FROM pessoas GROUP BY idade;
   ```

### 12. **HAVING**
   Usado para filtrar os resultados de uma consulta após a aplicação de `GROUP BY`. Similar ao `WHERE`, mas é usado com agregações.
   ```sql
   SELECT column_name, COUNT(*) FROM table_name
   GROUP BY column_name
   HAVING COUNT(*) > value;
   ```
   Exemplo:
   ```sql
   SELECT idade, COUNT(*) FROM pessoas
   GROUP BY idade
   HAVING COUNT(*) > 1;
   ```

### 13. **DISTINCT**
   Usado para retornar valores distintos/únicos.
   ```sql
   SELECT DISTINCT column_name FROM table_name;
   ```
   Exemplo:
   ```sql
   SELECT DISTINCT idade FROM pessoas;
   ```

### 14. **LIMIT**
   Usado para limitar o número de registros retornados.
   ```sql
   SELECT * FROM table_name LIMIT number;
   ```
   Exemplo:
   ```sql
   SELECT * FROM pessoas LIMIT 10;
   ```

### 15. **IN**
   Usado para especificar múltiplos valores em uma condição `WHERE`.
   ```sql
   SELECT * FROM table_name WHERE column_name IN (value1, value2, ...);
   ```
   Exemplo:
   ```sql
   SELECT * FROM pessoas WHERE idade IN (25, 30, 35);
   ```

### 16. **BETWEEN**
   Usado para filtrar valores dentro de um intervalo.
   ```sql
   SELECT * FROM table_name WHERE column_name BETWEEN value1 AND value2;
   ```
   Exemplo:
   ```sql
   SELECT * FROM pessoas WHERE idade BETWEEN 20 AND 30;
   ```

### 17. **LIKE**
   Usado para buscar um padrão em uma coluna.
   ```sql
   SELECT * FROM table_name WHERE column_name LIKE pattern;
   ```
   Exemplo:
   ```sql
   SELECT * FROM pessoas WHERE nome LIKE 'J%';
   ```

### 18. **UNION**
   Combina o resultado de duas ou mais consultas `SELECT`.
   ```sql
   SELECT column_name FROM table1
   UNION
   SELECT column_name FROM table2;
   ```
   Exemplo:
   ```sql
   SELECT nome FROM pessoas
   UNION
   SELECT nome FROM clientes;
   ```

### 19. **INDEX**
   Usado para criar um índice para otimizar a busca de dados.
   ```sql
   CREATE INDEX index_name ON table_name (column_name);
   ```
   Exemplo:
   ```sql
   CREATE INDEX idx_nome ON pessoas (nome);
   ```

### 20. **VIEW**
   Usado para criar uma visão virtual baseada no resultado de uma `SELECT`.
   ```sql
   CREATE VIEW view_name AS
   SELECT column1, column2 FROM table_name WHERE condition;
   ```
   Exemplo:
   ```sql
   CREATE VIEW pessoas_adultas AS
   SELECT nome, idade FROM pessoas WHERE idade >= 18;
   ```

Sim, há ainda mais comandos e funcionalidades avançadas em SQL! Vou continuar com alguns outros conceitos importantes, úteis para otimizar o desempenho, garantir integridade dos dados e trabalhar com cenários mais complexos:

### 21. **TRUNCATE**
   Remove todos os registros de uma tabela, mas mantém a estrutura da tabela.
   ```sql
   TRUNCATE TABLE table_name;
   ```
   Exemplo:
   ```sql
   TRUNCATE TABLE pessoas;
   ```

### 22. **FOREIGN KEY**
   Usado para definir um relacionamento entre duas tabelas. A `foreign key` garante a integridade referencial.
   ```sql
   ALTER TABLE table_name
   ADD CONSTRAINT fk_name FOREIGN KEY (column_name)
   REFERENCES other_table (column_name);
   ```
   Exemplo:
   ```sql
   ALTER TABLE pedidos
   ADD CONSTRAINT fk_cliente FOREIGN KEY (cliente_id)
   REFERENCES clientes (id);
   ```

### 23. **PRIMARY KEY**
   Define uma coluna ou conjunto de colunas como identificador único da tabela. Uma tabela só pode ter uma `primary key`.
   ```sql
   CREATE TABLE table_name (
       id INT PRIMARY KEY,
       column_name datatype
   );
   ```

### 24. **AUTO_INCREMENT**
   Usado em conjunto com `PRIMARY KEY` para gerar valores automáticos e sequenciais para uma coluna.
   ```sql
   CREATE TABLE pessoas (
       id INT AUTO_INCREMENT PRIMARY KEY,
       nome VARCHAR(100)
   );
   ```

### 25. **TRANSACTION**
   Usado para garantir que uma sequência de operações SQL seja executada de forma atômica, ou seja, todas as operações ocorrem ou nenhuma ocorre.
   - **START TRANSACTION** inicia uma transação.
   - **COMMIT** salva as alterações.
   - **ROLLBACK** desfaz as alterações se algo der errado.
   
   Exemplo:
   ```sql
   START TRANSACTION;
   UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
   UPDATE contas SET saldo = saldo + 100 WHERE id = 2;
   COMMIT;
   ```

### 26. **CASE**
   Usado para adicionar lógica condicional dentro de uma consulta `SELECT`.
   ```sql
   SELECT column_name,
       CASE
           WHEN condition THEN result
           ELSE result
       END
   FROM table_name;
   ```
   Exemplo:
   ```sql
   SELECT nome, 
       CASE 
           WHEN idade >= 18 THEN 'Adulto'
           ELSE 'Menor'
       END AS classificacao
   FROM pessoas;
   ```

### 27. **EXISTS**
   Usado para verificar a existência de registros em uma subconsulta.
   ```sql
   SELECT column_name FROM table_name WHERE EXISTS (subquery);
   ```
   Exemplo:
   ```sql
   SELECT nome FROM pessoas WHERE EXISTS (SELECT 1 FROM pedidos WHERE pedidos.cliente_id = pessoas.id);
   ```

### 28. **IS NULL / IS NOT NULL**
   Usado para verificar valores nulos.
   ```sql
   SELECT * FROM table_name WHERE column_name IS NULL;
   ```
   Exemplo:
   ```sql
   SELECT nome FROM pessoas WHERE email IS NULL;
   ```

### 29. **ALTER DATABASE**
   Usado para modificar as propriedades de um banco de dados existente.
   ```sql
   ALTER DATABASE database_name CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
   ```

### 30. **RENAME TABLE**
   Usado para renomear uma tabela.
   ```sql
   RENAME TABLE old_table_name TO new_table_name;
   ```
   Exemplo:
   ```sql
   RENAME TABLE pessoas TO usuarios;
   ```

### 31. **JOIN Types**
   Existem diferentes tipos de `JOIN`, cada um com seu comportamento específico:
   - **INNER JOIN**: Retorna registros que possuem correspondência em ambas as tabelas.
   - **LEFT JOIN**: Retorna todos os registros da tabela à esquerda e os registros correspondentes da tabela à direita. Se não houver correspondência, retorna `NULL` nos valores da tabela à direita.
   - **RIGHT JOIN**: O contrário do `LEFT JOIN`, retorna todos os registros da tabela à direita e os correspondentes da tabela à esquerda.
   - **FULL OUTER JOIN**: Retorna registros quando há correspondência em uma das tabelas, ou seja, inclui todos os registros de ambas as tabelas, preenchendo com `NULL` quando necessário.
   
   Exemplo de **LEFT JOIN**:
   ```sql
   SELECT p.nome, o.pedido_id 
   FROM pessoas p
   LEFT JOIN pedidos o ON p.id = o.cliente_id;
   ```

### 32. **FULL OUTER JOIN**
   Usado para combinar os resultados de duas tabelas e incluir todos os registros, independentemente de uma correspondência.
   ```sql
   SELECT * FROM table1
   FULL OUTER JOIN table2 ON table1.column_name = table2.column_name;
   ```

### 33. **SUBQUERY (Subconsulta)**
   Uma consulta dentro de outra consulta, que pode ser usada em comandos `SELECT`, `INSERT`, `UPDATE`, ou `DELETE`.
   ```sql
   SELECT nome FROM pessoas WHERE idade > (SELECT AVG(idade) FROM pessoas);
   ```

### 34. **COALESCE**
   Retorna o primeiro valor não nulo de uma lista.
   ```sql
   SELECT COALESCE(email, 'email não informado') FROM pessoas;
   ```

### 35. **SET**
   Usado para definir valores para variáveis ou para modificar valores em uma tabela.
   ```sql
   SET @min_age = (SELECT MIN(idade) FROM pessoas);
   ```

Esses são mais comandos e funcionalidades que ampliam bastante o poder do SQL para consultas e manipulação de dados. Muitos deles são úteis para otimizar o desempenho de consultas, garantir a integridade de dados e trabalhar com grandes volumes de informações.

Tem algum desses que você gostaria de explorar mais?