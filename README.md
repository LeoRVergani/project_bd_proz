```markdown
# Projeto Banco de Dados com Triggers no PostgreSQL

Este projeto demonstra a criação de um banco de dados no PostgreSQL, adicionando tabelas, inserindo dados e configurando triggers para operações específicas. 

## Estrutura do Projeto

1. **Criação do Banco de Dados e Tabelas:** `create_tables.sql`
2. **Inserção de Dados:** `insert_data.sql`
3. **Configuração de Triggers:** `triggers.sql`
4. **Consultas com Joins:** `queries.sql`

### 1. Criação do Banco de Dados e Tabelas

Crie um arquivo `create_tables.sql` com o seguinte conteúdo:

```sql
-- Criação do banco de dados
CREATE DATABASE projeto_bd;

-- Conectar ao banco de dados
\c projeto_bd;

-- Criar tabela de clientes
CREATE TABLE clientes (
    cliente_id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);

-- Criar tabela de produtos
CREATE TABLE produtos (
    produto_id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL
);

-- Criar tabela de pedidos
CREATE TABLE pedidos (
    pedido_id SERIAL PRIMARY KEY,
    cliente_id INT REFERENCES clientes(cliente_id),
    produto_id INT REFERENCES produtos(produto_id),
    quantidade INT NOT NULL,
    data_pedido DATE NOT NULL DEFAULT CURRENT_DATE
);

-- Criar tabela de log de pedidos
CREATE TABLE log_pedidos (
    log_id SERIAL PRIMARY KEY,
    pedido_id INT,
    data_insercao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 2. Inserção de Dados

Crie um arquivo `insert_data.sql` com o seguinte conteúdo:

```sql
-- Inserir dados na tabela de clientes
INSERT INTO clientes (nome, email) VALUES
('João Silva', 'joao.silva@example.com'),
('Maria Oliveira', 'maria.oliveira@example.com');

-- Inserir dados na tabela de produtos
INSERT INTO produtos (nome, preco) VALUES
('Produto A', 10.50),
('Produto B', 20.00);

-- Inserir dados na tabela de pedidos
INSERT INTO pedidos (cliente_id, produto_id, quantidade) VALUES
(1, 1, 2),
(2, 2, 1),
(1, 2, 3);
```

### 3. Configuração de Triggers

Crie um arquivo `triggers.sql` com o seguinte conteúdo:

```sql
-- Função que será chamada pelo trigger
CREATE OR REPLACE FUNCTION log_pedido_insert()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO log_pedidos (pedido_id) VALUES (NEW.pedido_id);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Trigger que chama a função após um insert na tabela de pedidos
CREATE TRIGGER after_pedido_insert
AFTER INSERT ON pedidos
FOR EACH ROW
EXECUTE FUNCTION log_pedido_insert();
```

### 4. Consultas com Joins

Crie um arquivo `queries.sql` com o seguinte conteúdo:

```sql
-- Consulta para obter todos os pedidos com detalhes dos clientes e produtos
SELECT 
    p.pedido_id,
    c.nome AS cliente_nome,
    pr.nome AS produto_nome,
    p.quantidade,
    p.data_pedido
FROM 
    pedidos p
JOIN 
    clientes c ON p.cliente_id = c.cliente_id
JOIN 
    produtos pr ON p.produto_id = pr.produto_id;

-- Consulta para obter a soma total dos pedidos por cliente
SELECT 
    c.nome AS cliente_nome,
    SUM(pr.preco * p.quantidade) AS total_gasto
FROM 
    pedidos p
JOIN 
    clientes c ON p.cliente_id = c.cliente_id
JOIN 
    produtos pr ON p.produto_id = pr.produto_id
GROUP BY 
    c.nome;
```

## Executar os Scripts no PostgreSQL

Para executar esses arquivos no PostgreSQL, você pode utilizar o comando `psql` no terminal:

```bash
# Conectar ao PostgreSQL
psql -U seu_usuario -d postgres

# Executar o script de criação das tabelas
\i /caminho/para/seu/arquivo/create_tables.sql

# Executar o script de inserção de dados
\i /caminho/para/seu/arquivo/insert_data.sql

# Executar o script de criação do trigger
\i /caminho/para/seu/arquivo/triggers.sql

# Executar o script de consultas
\i /caminho/para/seu/arquivo/queries.sql
```

## Subir o Projeto para o GitHub

Depois de criar os arquivos `.sql`, siga os passos abaixo para inicializar o repositório Git, adicionar os arquivos, fazer commit e subir para o GitHub:

```bash
# Navegar até o diretório do projeto
cd caminho/para/seu/projeto

# Inicializar um repositório Git
git init

# Adicionar os arquivos ao repositório
git add .

# Fazer commit das mudanças
git commit -m "Inicializar projeto com script SQL"

# Adicionar o repositório remoto (substitua pelo seu URL do GitHub)
git remote add origin https://github.com/seu-usuario/projeto_bd.git

# Subir o projeto para o GitHub
git push -u origin master
```
