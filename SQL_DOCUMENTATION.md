# Sistema de Gerenciamento de Biblioteca - Documentação do Banco de Dados

## Esquema do Banco de Dados

### Tabelas Principais

#### 1. Authors (Autores)
```sql
CREATE TABLE authors (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    nationality VARCHAR(100),
    birth_date DATE,
    biography VARCHAR(1000)
);
```

#### 2. Categories (Categorias)
```sql
CREATE TABLE categories (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE,
    description VARCHAR(500)
);
```

#### 3. Books (Livros)
```sql
CREATE TABLE books (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    isbn VARCHAR(20) NOT NULL UNIQUE,
    title VARCHAR(200) NOT NULL,
    description VARCHAR(1000),
    publication_date DATE NOT NULL,
    total_copies INT NOT NULL,
    available_copies INT NOT NULL,
    author_id BIGINT NOT NULL,
    FOREIGN KEY (author_id) REFERENCES authors(id)
);
```

#### 4. Members (Membros)
```sql
CREATE TABLE members (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    email VARCHAR(150) NOT NULL UNIQUE,
    phone VARCHAR(20),
    address VARCHAR(200),
    membership_date DATE NOT NULL,
    status ENUM('ACTIVE', 'SUSPENDED', 'EXPIRED') NOT NULL
);
```

#### 5. Loans (Empréstimos)
```sql
CREATE TABLE loans (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    loan_date DATE NOT NULL,
    due_date DATE NOT NULL,
    return_date DATE,
    status ENUM('ACTIVE', 'RETURNED', 'OVERDUE') NOT NULL,
    notes VARCHAR(500),
    book_id BIGINT NOT NULL,
    member_id BIGINT NOT NULL,
    FOREIGN KEY (book_id) REFERENCES books(id),
    FOREIGN KEY (member_id) REFERENCES members(id)
);
```

#### 6. Book_Categories (Tabela de Junção)
```sql
CREATE TABLE book_categories (
    book_id BIGINT NOT NULL,
    category_id BIGINT NOT NULL,
    PRIMARY KEY (book_id, category_id),
    FOREIGN KEY (book_id) REFERENCES books(id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE CASCADE
);
```

## Relacionamentos entre Entidades

### Diagrama de Relacionamentos
![Diagrama entidade-relacionamento do banco de dados mostrando as tabelas do sistema de biblioteca (Autores, Livros, Categorias, Membros, Empréstimos, Categorias_Livros) com seus respectivos campos e conexões. Chaves primárias e estrangeiras são destacadas, ilustrando os relacionamentos um-para-muitos e muitos-para-muitos entre entidades.](sql_diagram.svg)

### Relacionamentos Detalhados

1. **Authors → Books** (Um-para-Muitos)
   - Um autor pode escrever múltiplos livros
   - Cada livro tem exatamente um autor

2. **Books ↔ Categories** (Muitos-para-Muitos)
   - Um livro pode pertencer a múltiplas categorias
   - Uma categoria pode conter múltiplos livros
   - Implementado via tabela `book_categories`

3. **Members → Loans** (Um-para-Muitos)
   - Um membro pode ter múltiplos empréstimos
   - Cada empréstimo pertence a exatamente um membro

4. **Books → Loans** (Um-para-Muitos)
   - Um livro pode ter múltiplos registros de empréstimo
   - Cada empréstimo é para exatamente um livro

## Regras de Negócio Principais

- **Empréstimos**: Máximo 5 empréstimos ativos por membro
- **Período**: 14 dias de empréstimo por padrão
- **Disponibilidade**: Apenas livros com `available_copies > 0` podem ser emprestados
- **Status**: Apenas membros `ACTIVE` podem pegar livros emprestados
- **Unicidade**: ISBN único por livro, email único por membro, nome único por categoria

## Exemplos Práticos de Uso

Para exemplos detalhados de como interagir com o banco de dados através da API REST, incluindo cenários de teste completos e fluxos práticos, consulte:
- **[api_endpoints_test.txt](api_endpoints_test.txt)** - Contém exemplos reais de consultas, inserções, atualizações e operações complexas