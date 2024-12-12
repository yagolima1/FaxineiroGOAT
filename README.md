# O Faxineiro Implacável
"Quando uma mancha indesejada aparece, ele não mede esforços para removê-la, mesmo que isso signifique desafiar as leis dos dados."

Este repositório contém a atividade *O Faxineiro Implacável*, desenvolvida para aprimorar habilidades em SQL, especialmente na identificação e correção de erros em scripts e na normalização de dados.

## Objetivo
A atividade tem como objetivo treinar a capacidade de análise de erros e organizar um banco de dados utilizando comandos de manipulação e alteração de tabelas. Ao longo do exercício, vamos realizar correções, ajustes de estrutura e organizar as informações em novas tabelas para otimizar o armazenamento de dados.

## Descrição da Atividade
A atividade consiste em criar um banco de dados chamado `Biblioteca` e lidar com uma tabela que contém erros em sua estrutura. Precisamos corrigir esses erros para que a tabela seja renderizada corretamente, normalizar a base separando dados relacionados em novas tabelas, e adequar a estrutura para evitar redundâncias.

Com o banco de dados estruturado, vamos praticar comandos como `ALTER TABLE`, `DROP`, `IF EXISTS`, `IF NOT EXISTS`, entre outros, para organizar, adicionar novas referências e remover informações redundantes. Também será necessário ajustar a inserção de dados na tabela já corrigida para garantir que todos os dados estejam consistentes com o novo modelo de dados.

### 1. Criando o Banco de Dados

```sql
CREATE DATABASE IF NOT EXISTS Biblioteca;
USE Biblioteca;
```

### 2. Criando a Tabela `Livros` (Primeira Versão)

Na primeira versão, criamos a tabela `Livros` com as colunas básicas e inserimos os dados iniciais.

```sql
DROP TABLE IF EXISTS Livros;
CREATE TABLE Livros (
    livros_id INT PRIMARY KEY,
    titulo VARCHAR(255),
    autor VARCHAR(100),
    editora VARCHAR(100),
    ano_publicacao INT,
    isbn VARCHAR(20)
);

INSERT INTO Livros (livros_id, titulo, autor, editora, ano_publicacao, isbn) VALUES 
(1, 'A Culpa é das Estrelas', 'John Green', 'Intrínseca', 2012, '978-85-8057-346-6'),
(2, 'Harry Potter e a Pedra Filosofal', 'J.K. Rowling', 'Rocco', 1997, '9788532511010'),
(3, 'O Senhor dos Anéis: A Sociedade do Anel', 'J.R.R. Tolkien', 'Martins Fontes', 1954, '9788533603149'),
(4, 'The Catcher in the Rye', 'J.D. Salinger', 'Little, Brown and Company', '1951', '9780316769488'),
(5, '1984', 'George Orwell', 'Companhia Editora Nacional', 1949, '978-85-221-0616-9'),
(6, 'Percy Jackson e o Ladrão de Raios', 'Rick Riordan', 'Intrínseca', 2005, '9788598078355');
```

### 3. Otimizando a Chave Primária com AUTO_INCREMENT

Para que `livros_id` seja preenchido automaticamente, modificamos a coluna para `AUTO_INCREMENT`.

```sql
ALTER TABLE Livros 
MODIFY livros_id INT AUTO_INCREMENT;
```

### 4. Criando as Tabelas `Autores` e `Editoras`

Para normalizar a base de dados, criamos as tabelas `Autores` e `Editoras`, cada uma com uma chave primária `AUTO_INCREMENT` para armazenar as informações específicas.

```sql
CREATE TABLE IF NOT EXISTS Autores (
    autor_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    nacionalidade VARCHAR(100),
    data_nascimento DATE
);

CREATE TABLE IF NOT EXISTS Editoras (
    editora_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    cnpj VARCHAR(20),
    cidade VARCHAR(100),
    pais VARCHAR(100)
);
```

### 5. Alterando a Tabela `Livros` para Referenciar `Autores` e `Editoras`

Removemos as colunas `autor` e `editora` da tabela `Livros` e adicionamos as colunas `autor_id` e `editora_id` como chaves estrangeiras.

```sql
ALTER TABLE Livros
DROP COLUMN autor;
ALTER TABLE Livros
DROP COLUMN editora;

ALTER TABLE Livros
ADD COLUMN autor_id INT,
ADD COLUMN editora_id INT;

ALTER TABLE Livros
ADD CONSTRAINT fk_autor_id FOREIGN KEY (autor_id) REFERENCES Autores(autor_id),
ADD CONSTRAINT fk_editora_id FOREIGN KEY (editora_id) REFERENCES Editoras(editora_id);
```

### 6. Inserindo Dados nas Tabelas `Autores` e `Editoras`

Para preencher as tabelas `Autores` e `Editoras` com os dados dos livros iniciais, utilizamos os seguintes scripts:

```sql
INSERT INTO Autores (nome, nacionalidade, data_nascimento) VALUES
('John Green', 'Americana', '1977-08-24'),
('J.K. Rowling', 'Britânica', '1965-07-31'),
('J.R.R. Tolkien', 'Britânica', '1892-01-03'),
('J.D. Salinger', 'Americana', '1919-01-01'),
('George Orwell', 'Britânica', '1903-06-25'),
('Rick Riordan', 'Americana', '1964-06-05'),
('João Guimarães Rosa', 'Brasileira', '1908-06-27'),
('Machado de Assis', 'Brasileira', '1839-06-21'),
('Graciliano Ramos', 'Brasileira', '1892-10-27'),
('Aluísio Azevedo', 'Brasileira', '1857-04-14'),
('Mário de Andrade', 'Brasileira', '1893-10-09');

INSERT INTO Editoras (nome, cnpj, cidade, pais) VALUES
('Intrínseca', '12345678901234', 'Rio de Janeiro', 'Brasil'),
('Rocco', '23456789012345', 'Rio de Janeiro', 'Brasil'),
('Martins Fontes', '34567890123456', 'São Paulo', 'Brasil'),
('Little, Brown and Company', '45678901234567', 'Nova York', 'Estados Unidos'),
('Companhia Editora Nacional', '56789012345678', 'São Paulo', 'Brasil'),
('Nova Fronteira', '67890123456789', 'Rio de Janeiro', 'Brasil'),
('Companhia das Letras', '78901234567890', 'São Paulo', 'Brasil'),
('Penguin Companhia', '89012345678901', 'São Paulo', 'Brasil'),
('Martin Claret', '90123456789012', 'São Paulo', 'Brasil');
```

### 7. Inserindo Livros Referenciando `Autores` e `Editoras`

Após as mudanças, os dados dos livros foram inseridos usando os IDs das tabelas `Autores` e `Editoras`.

```sql
INSERT INTO Livros (titulo, ano_publicacao, isbn, autor_id, editora_id) VALUES 
('A Culpa é das Estrelas', 2012, '978-85-8057-346-6', 1, 1),
('Harry Potter e a Pedra Filosofal', 1997, '9788532511010', 2, 2),
('O Senhor dos Anéis: A Sociedade do Anel', 1954, '9788533603149', 3, 3),
('The Catcher in the Rye', 1951, '9780316769488', 4, 4),
('1984', 1949, '978-85-221-0616-9', 5, 5),
('Percy Jackson e o Ladrão de Raios', 2005, '9788598078355', 6, 1),
('Grande Sertão: Veredas', 1956, '978-85-209-2325-1', 7, 6),
('Memórias Póstumas de Brás Cubas', 1881, '9788535910663', 8, 7),
('Vidas Secas', 1938, '9788572326972', 9, 5),
('O Alienista', 1882, '9788572327429', 8, 8),
('O Cortiço', 1890, '9788579027048', 10, 9),
('Dom Casmurro', 1899, '9788583862093', 8, 9),
('Macunaíma', 1928, '9788503012302', 11, 5);
```

## Como Executar
1. Clone este repositório.
2. Importe o script SQL no banco de dados.
3. Utilize o MySQL Workbench para realizar e testar os comandos SQL.

## Tecnologias Utilizadas
- MySQL Workbench
- SQL

## Autor
Este projeto foi desenvolvido como parte de uma atividade de prática em SQL, com foco na normalização e organização de dados, proposto pelo Professor Gabriel.

---
