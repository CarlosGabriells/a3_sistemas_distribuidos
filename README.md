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
- **Script de População de Dados** para configuração inicial com dados de amostra

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

- `test`: Banco de dados H2 em memória para testes
