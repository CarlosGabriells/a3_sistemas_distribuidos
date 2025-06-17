# Sistema de Gerenciamento de Biblioteca

Um sistema de gerenciamento de biblioteca abrangente e pronto para produção, construído com Spring Boot, seguindo princípios de arquitetura limpa, melhores práticas da indústria e convenções de nomenclatura Java adequadas (`com.example.librarymanagement`). Possui operações CRUD completas, validação de lógica de negócios, testes automatizados e documentação extensa.

## 🚀 Funcionalidades

- **Operações CRUD Completas** para todas as entidades (Livros, Autores, Membros, Categorias, Empréstimos)
- **Busca Avançada** com paginação e ordenação
- **Gerenciamento de Relacionamentos** entre entidades (@OneToMany, @ManyToOne, @ManyToMany)
- **Validação de Lógica de Negócio** (limites de empréstimo, disponibilidade de livros, status de membros)
- **Tratamento Global de Exceções** com respostas de erro significativas
- **Documentação Automática da API** com Swagger/OpenAPI
- **Objetos de Transferência de Dados (DTOs)** com anotações de validação
- **Arquitetura Limpa** com estrutura em camadas
- **Script de População de Dados** para configuração inicial com dados de amostra
- **Documentação Abrangente** incluindo uso da API e esquema SQL
- **Suporte Docker** com docker-compose para deploy fácil
- **Gerenciamento de Dados de Amostra** com dados de teste realistas
- **Melhores Práticas JPQL/JPA** (sem SQL nativo)

## 🏗️ Arquitetura

A aplicação segue uma arquitetura limpa e em camadas:

```
├── Camada Controller    # Manipulação de requisições HTTP
├── Camada Service       # Lógica de negócio e validação
├── Camada Repository    # Acesso a dados com Spring Data JPA
├── Camada Entity        # Entidades JPA com relacionamentos
├── Camada DTO           # Objetos de transferência de dados com validação
├── Camada Mapper        # Mapeamento Entity-DTO com MapStruct
└── Camada Exception     # Tratamento global de exceções
```

## 🛠️ Stack Tecnológica

- **Java 17**
- **Spring Boot 3.3.0**
- **Spring Data JPA** com Hibernate
- **MySQL** banco de dados
- **Lombok** para redução de código repetitivo
- **MapStruct** para mapeamento entity-DTO
- **Swagger/OpenAPI** para documentação da API
- **Maven** para gerenciamento de dependências

## 📋 Pré-requisitos

- **Docker** e **Docker Compose** (método recomendado)
- Alternativamente: Java 17+, Maven 3.6+, MySQL 8.0+

## 🚀 Começando

### 1. Clonar o Repositório

```bash
git clone <repository-url>
cd library-management-system
```

### 2. 🐳 Executar com Docker (Recomendado)

```bash
# Construir e executar com MySQL
docker-compose up -d

# Popular banco de dados com dados de amostra
./scripts/populate-database.sh
```

### 3. Acessar a Aplicação

- **Aplicação**: http://localhost:8080
- **Documentação da API**: http://localhost:8080/swagger-ui.html
- **Docs da API JSON**: http://localhost:8080/api-docs

## ⚙️ Configuração Manual (Alternativa)

### Configuração do Banco de Dados

Criar um banco de dados MySQL:

```sql
CREATE DATABASE library_management;
```

Atualizar credenciais do banco em `src/main/resources/application.yml`:

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/library_management?createDatabaseIfNotExist=true&useSSL=false&serverTimezone=UTC
    username: seu_usuario
    password: sua_senha
```

### Executar a Aplicação

Usando Maven:

```bash
# Instalar dependências e executar
mvn clean install
mvn spring-boot:run
```

Usando IDE:
- Importar o projeto como projeto Maven
- Executar a classe principal: `LibraryManagementSystemApplication`

### Popular Dados de Amostra

```bash
# Popular banco de dados com dados de amostra
./scripts/populate-database.sh
```

## 🐳 Configuração Docker Avançada

### Docker Compose com Customização

```bash
# Construir e executar em background
docker-compose up -d

# Ver logs
docker-compose logs -f

# Parar serviços
docker-compose down
```

### Configuração Docker Manual

```bash
# Construir a aplicação
mvn clean package -DskipTests

# Construir imagem Docker
docker build -t library-management-system .

# Executar container MySQL
docker run --name mysql-library -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=library_management -p 3306:3306 -d mysql:8.0

# Executar container da aplicação
docker run --name library-app --link mysql-library:mysql -p 8080:8080 -d library-management-system
```

## 📊 Esquema do Banco de Dados

### Entidades e Relacionamentos

1. **Author** ←→ **Book** (Um-para-Muitos)
2. **Book** ←→ **Category** (Muitos-para-Muitos)
3. **Book** ←→ **Loan** (Um-para-Muitos)
4. **Member** ←→ **Loan** (Um-para-Muitos)

### Características Principais

- **Operações em Cascata**: Configurações adequadas de cascata para entidades relacionadas
- **Carregamento Lazy**: Busca otimizada de dados com carregamento lazy
- **Validação**: Validação abrangente nos níveis de entidade e DTO
- **Indexação**: Indexação adequada do banco para performance
- **Consultas JPQL**: Todas as operações de banco usam JPQL (sem SQL nativo)
- **Dados de Amostra**: Dados de amostra abrangentes com 8 autores, 12 livros, 10 membros, 8 categorias e 9 empréstimos

### Documentação

- **[SQL_DOCUMENTATION.md](SQL_DOCUMENTATION.md)**: Esquema completo do banco, relacionamentos e todas as consultas

## 🔌 API REST

### Entidades Principais
- **Autores** (`/api/authors`) - 7 endpoints para gerenciamento de autores
- **Livros** (`/api/books`) - 8 endpoints com busca por ISBN, categoria e disponibilidade
- **Membros** (`/api/members`) - 9 endpoints com gerenciamento de status e empréstimos
- **Categorias** (`/api/categories`) - 8 endpoints para classificação de livros
- **Empréstimos** (`/api/loans`) - 11 endpoints com controle de devolução e atraso

### Documentação Completa
- **[api_endpoints_test.txt](api_endpoints_test.txt)** - Guia completo com todos os 43 endpoints, exemplos práticos e fluxos de teste
- **Swagger UI** - http://localhost:8080/swagger-ui.html (documentação interativa)

## 💡 Regras de Negócio

### Gerenciamento de Empréstimos
- Máximo de 5 empréstimos ativos por membro
- Período padrão de empréstimo: 14 dias
- Apenas membros ativos podem pegar livros emprestados
- Livros devem estar disponíveis (availableCopies > 0)
- Detecção automática de atraso e atualização de status

### Regras de Validação
- ISBN único para livros
- Email único para membros
- Nomes únicos de categorias
- Validação de formato ISBN
- Validação de formato de email
- Validação de formato de telefone
- Validações de data (data de nascimento, data de publicação, etc.)


## 📝 Dados de Amostra

A aplicação inclui população automatizada de dados de amostra com dados realistas de biblioteca:

### Dados de Amostra Incluídos

- **8 Autores**: J.K. Rowling, George Orwell, Harper Lee, Agatha Christie, Stephen King, Jane Austen, Ernest Hemingway, Gabriel García Márquez
- **12 Livros**: Literatura clássica incluindo série Harry Potter, 1984, O Sol é Para Todos, etc.
- **8 Categorias**: Fantasia, Ficção Científica, Mistério, Literatura Clássica, Terror, Romance, Ficção Histórica, Distópico
- **10 Membros**: Mistura de membros ativos e suspensos com perfis realistas
- **9 Empréstimos**: Empréstimos ativos, devolvidos e em atraso demonstrando regras de negócio

### População de Dados

```bash
# Popular banco de dados com dados de amostra
./scripts/populate-database.sh

### Criação Manual de Dados

Você também pode usar a API para criar dados de amostra:

### Criar um Autor
```json
POST /api/authors
{
  "firstName": "J.K.",
  "lastName": "Rowling",
  "nationality": "Britânica",
  "birthDate": "1965-07-31",
  "biography": "Autora britânica, mais conhecida pela série Harry Potter"
}
```

### Criar uma Categoria
```json
POST /api/categories
{
  "name": "Fantasia",
  "description": "Literatura e ficção fantástica"
}
```

### Criar um Livro
```json
POST /api/books
{
  "isbn": "9780439358071",
  "title": "Harry Potter e a Ordem da Fênix",
  "description": "O quinto romance da série Harry Potter",
  "publicationDate": "2003-06-21",
  "totalCopies": 10,
  "availableCopies": 10,
  "authorId": 1,
  "categoryIds": [1]
}
```

## 🔧 Configuração

### Propriedades da Aplicação

Principais opções de configuração em `application.yml`:

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/library_management
    username: root
    password: root
  
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
  
server:
  port: 8080

logging:
  level:
    org.hibernate.SQL: DEBUG
```

### Perfis

- `default`: Configuração MySQL
- `test`: Banco de dados H2 em memória para testes
