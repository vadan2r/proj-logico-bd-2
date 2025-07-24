# proj-logico-bd-2
Projeto 2 - DIO - Construa um Projeto Lógico de Banco de Dados do Zero

# Projeto Lógico de Banco de Dados para Oficina

Este documento detalha o passo a passo para construir um projeto lógico de banco de dados, utilizando como base um esquema conceitual (criado no modelo ER) para o contexto de uma oficina, e implementando-o em um modelo relacional.

## 1. Esquema Conceitual e Lógico

*   **Utilizar o esquema conceitual existente (modelo ER):** O projeto se baseia em um esquema conceitual já criado.  Esse esquema representa as entidades, atributos e relacionamentos relevantes para uma oficina.

*   **Definir todas as etapas:** Desde o esquema até a implementação, todas as etapas devem ser definidas e realizadas.

*   **Modelagem no Modelo Relacional:** O esquema lógico deve ser modelado utilizando o modelo relacional.

## 2. Criação do Script SQL

Após a definição do esquema lógico, crie o script SQL para a criação do esquema do banco de dados.

**Exemplo Genérico (Adaptar ao seu esquema lógico de oficina):**

```sql
-- Criação da Tabela Clientes
CREATE TABLE Clientes (
    ID INT PRIMARY KEY,
    Nome VARCHAR(255),
    Endereco VARCHAR(255),
    Telefone VARCHAR(20)
);

-- Criação da Tabela Veiculos
CREATE TABLE Veiculos (
    ID INT PRIMARY KEY,
    ClienteID INT,
    Marca VARCHAR(255),
    Modelo VARCHAR(255),
    Placa VARCHAR(10),
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ID)
);

-- Criação da Tabela Servicos
CREATE TABLE Servicos (
    ID INT PRIMARY KEY,
    Descricao VARCHAR(255),
    Preco DECIMAL(10, 2)
);

-- Criação da Tabela OrdensServico
CREATE TABLE OrdensServico (
    ID INT PRIMARY KEY,
    VeiculoID INT,
    DataEntrada DATE,
    DataSaida DATE,
    DescricaoProblema TEXT,
    FOREIGN KEY (VeiculoID) REFERENCES Veiculos(ID)
);

-- Criação da Tabela ItensOrdemServico (relacionamento N:M entre OrdensServico e Servicos)
CREATE TABLE ItensOrdemServico (
    OrdemServicoID INT,
    ServicoID INT,
    Quantidade INT,
    PRIMARY KEY (OrdemServicoID, ServicoID),
    FOREIGN KEY (OrdemServicoID) REFERENCES OrdensServico(ID),
    FOREIGN KEY (ServicoID) REFERENCES Servicos(ID)
);
```

## 3. Persistência de Dados

Realize a persistência de dados para a realização de testes.

**Exemplo Genérico (Adaptar aos seus dados):**

```sql
-- Inserção de dados na tabela Clientes
INSERT INTO Clientes (ID, Nome, Endereco, Telefone) VALUES
(1, 'João da Silva', 'Rua A, 123', '11-1234-5678'),
(2, 'Maria Souza', 'Avenida B, 456', '21-9876-5432');

-- Inserção de dados na tabela Veiculos
INSERT INTO Veiculos (ID, ClienteID, Marca, Modelo, Placa) VALUES
(1, 1, 'Fiat', 'Palio', 'ABC1234'),
(2, 2, 'Volkswagen', 'Gol', 'DEF5678');

-- Inserção de dados na tabela Servicos
INSERT INTO Servicos (ID, Descricao, Preco) VALUES
(1, 'Troca de Óleo', 150.00),
(2, 'Alinhamento', 80.00);

-- Inserção de dados na tabela OrdensServico
INSERT INTO OrdensServico (ID, VeiculoID, DataEntrada, DataSaida, DescricaoProblema) VALUES
(1, 1, '2023-10-26', '2023-10-27', 'Troca de óleo e filtro.');

-- Inserção de dados na tabela ItensOrdemServico
INSERT INTO ItensOrdemServico (OrdemServicoID, ServicoID, Quantidade) VALUES
(1, 1, 1); -- Troca de Óleo (Serviço ID 1) na Ordem de Serviço ID 1
```

## 4. Queries SQL Mais Complexas

Especifique queries mais complexas do que as apresentadas durante a explicação do desafio. As queries devem utilizar as seguintes cláusulas:

*   **Recuperações Simples com SELECT Statement**
*   **Filtros com WHERE Statement**
*   **Crie expressões para gerar atributos derivados**
*   **Defina ordenações dos dados com ORDER BY**
*   **Condições de filtros aos grupos – HAVING Statement**
*   **Crie junções entre tabelas para fornecer uma perspectiva mais complexa dos dados**

### Exemplos de Queries

1.  **Listar todos os clientes e seus respectivos veículos:**

    ```sql
    SELECT c.Nome, v.Marca, v.Modelo, v.Placa
    FROM Clientes c
    JOIN Veiculos v ON c.ID = v.ClienteID;
    ```

2.  **Encontrar todas as ordens de serviço para um determinado veículo (placa):**

    ```sql
    SELECT os.ID, os.DataEntrada, os.DataSaida, os.DescricaoProblema
    FROM OrdensServico os
    JOIN Veiculos v ON os.VeiculoID = v.ID
    WHERE v.Placa = 'ABC1234';
    ```

3.  **Calcular o valor total de uma ordem de serviço:**

    ```sql
    SELECT
        os.ID,
        SUM(s.Preco * ios.Quantidade) AS ValorTotal
    FROM
        OrdensServico os
    JOIN
        ItensOrdemServico ios ON os.ID = ios.OrdemServicoID
    JOIN
        Servicos s ON ios.ServicoID = s.ID
    WHERE
        os.ID = 1 -- Exemplo: Ordem de Serviço ID 1
    GROUP BY
        os.ID;
    ```

4.  **Listar os serviços mais requisitados:**

    ```sql
    SELECT s.Descricao, COUNT(*) AS Quantidade
    FROM Servicos s
    JOIN ItensOrdemServico ios ON s.ID = ios.ServicoID
    GROUP BY s.Descricao
    ORDER BY Quantidade DESC;
    ```

5.  **Clientes que possuem mais de um veículo:**

    ```sql
    SELECT c.Nome, COUNT(v.ID) AS NumeroVeiculos
    FROM Clientes c
    JOIN Veiculos v ON c.ID = v.ClienteID
    GROUP BY c.Nome
    HAVING COUNT(v.ID) > 1;
    ```

Este documento fornece uma estrutura para a construção do banco de dados da oficina. 
Adapte os exemplos ao seu esquema e adicione novas queries para melhor atender às necessidades do seu projeto.
