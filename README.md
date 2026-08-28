# 📊 Carteira de Investimentos Fullstack em Rust

Uma aplicação completa de gerenciamento e acompanhamento de carteira de investimentos desenvolvida em **Rust**, conectando back-end assíncrono com **Axum**, persistência relacional no **PostgreSQL** via **SQLx**, segurança com **Argon2** e **JWT**, e renderização server-side (SSR) de alta performance com **Askama**.

---

## 🚀 Tecnologias Utilizadas

- **Linguagem**: [Rust 2021 Edition](https://www.rust-lang.org/)
- **Framework Web**: [Axum 0.7](https://github.com/tokio-rs/axum)
- **Runtime Assíncrono**: [Tokio](https://tokio.rs/)
- **Banco de Dados**: [PostgreSQL 16](https://www.postgresql.org/) com [Docker Compose](https://docs.docker.com/compose/)
- **Driver / Migrations**: [SQLx](https://github.com/launchbadge/sqlx) com queries assíncronas e migrations automáticas
- **Template Engine**: [Askama](https://github.com/djc/askama) (HTML com compilação estática em tempo de build)
- **Autenticação & Criptografia**: [jsonwebtoken](https://github.com/Keats/rust-jwt) + [Argon2](https://github.com/RustCrypto/password-hashes) + Cookies `HttpOnly`
- **Serialização**: [Serde](https://serde.rs/) & [serde_json](https://github.com/serde-rs/json)
- **Interface & Estilização**: CSS Moderno e Responsivo com Dark Theme de alto contraste.

---

## 🎯 Funcionalidades e Melhorias Implementadas

1. **Autenticação Completa e Segura**:
   - Cadastro e Login de usuários com hash de senhas via **Argon2**.
   - Sessão mantida via **JWT** assinado com armazenamento seguro em cookies `HttpOnly` ou cabeçalhos `Authorization: Bearer`.
   - Isolamento de dados: cada usuário visualiza e gerencia estritamente sua própria carteira.

2. **Gestão de Ativos (CRUD Completo)**:
   - Cadastro de ativos: Ações, Fundos Imobiliários (FIIs), ETFs, Criptomoedas, Renda Fixa e BDRs.
   - Campos de controle: Ticker, Nome, Categoria, Quantidade/Cotas, Preço Médio e Preço Atual de Mercado.
   - Edição e exclusão dinâmica de posições com confirmação de segurança.

3. **Cálculo Automático de Patrimônio & Rentabilidade**:
   - Valor Total Investido (`quantidade * preço médio`).
   - Valor Total de Mercado Atual (`quantidade * preço atual`).
   - Lucro/Prejuízo nominal (R$) e percentual (%) por ativo e consolidado no dashboard.
   - Cards de métricas financeiras em tempo real.

4. **Dupla Interface**:
   - **Interface Web (SSR)**: Páginas HTML renderizadas de forma ultrarrápida com Askama.
   - **API RESTful JSON**: Endpoints completos para integração mobile ou front-end SPA.

---

## 📋 Arquitetura do Projeto

```
carteira-investimentos/
├── .github/workflows/
│   └── ci.yml                 # Pipeline CI (fmt, check)
├── migrations/
│   └── 001_create_tables.sql  # Schema SQL do banco de dados
├── src/
│   ├── auth/
│   │   ├── jwt.rs             # Criptografia, Argon2 e JWT
│   │   ├── middleware.rs      # Extrator Axum para rotas protegidas
│   │   └── mod.rs
│   ├── models/
│   │   ├── user.rs            # Structs de usuário e DTOs de auth
│   │   ├── asset.rs           # Structs de ativos e cálculos de carteira
│   │   └── mod.rs
│   ├── routes/
│   │   ├── api.rs             # Rotas da API REST (JSON)
│   │   ├── web.rs             # Rotas Web (HTML / Askama)
│   │   └── mod.rs
│   ├── templates/
│   │   └── mod.rs             # Structs de templates Askama
│   ├── config.rs              # Carregamento de variáveis de ambiente
│   ├── db.rs                  # Pool de conexões SQLx
│   ├── error.rs               # Tratamento centralizado de erros (AppError)
│   └── main.rs                # Ponto de entrada e configuração do servidor
├── static/
│   └── style.css              # Folha de estilos Dark Theme
├── templates/                 # Arquivos HTML dos templates Askama
│   ├── base.html
│   ├── home.html
│   ├── login.html
│   ├── register.html
│   ├── dashboard.html
│   ├── asset_form.html
│   └── asset_edit.html
├── .env.example
├── .gitignore
├── Cargo.toml
├── compose.yml
└── README.md
```

---

## ⚙️ Como Executar o Projeto Localmente

### 1. Pré-requisitos
- [Rust & Cargo](https://rustup.rs/) (versão estável mais recente)
- [Docker & Docker Compose](https://www.docker.com/)

### 2. Clonar o Repositório
```bash
git clone https://github.com/felipeandrewpro/Carteira-De-Investimentos.git
cd Carteira-De-Investimentos
```

### 3. Subir o Banco de Dados PostgreSQL
```bash
docker compose up -d
```

### 4. Configurar as Variáveis de Ambiente
Crie um arquivo `.env` baseado no `.env.example`:
```bash
cp .env.example .env
```

### 5. Compilar e Rodar a Aplicação
```bash
cargo run
```

A aplicação estará disponível em:
👉 **http://localhost:3000**

---

## 📡 Endpoints da API REST

| Método | Rota | Descrição | Autenticação |
|---|---|---|---|
| `POST` | `/api/register` | Cadastra um novo usuário | Pública |
| `POST` | `/api/login` | Realiza login e retorna token JWT | Pública |
| `GET` | `/api/me` | Retorna dados do usuário autenticado | Requer JWT |
| `GET` | `/api/assets` | Lista todos os ativos do usuário | Requer JWT |
| `POST` | `/api/assets` | Cria um novo ativo na carteira | Requer JWT |
| `GET` | `/api/assets/:id` | Retorna detalhes de um ativo específico | Requer JWT |
| `PUT` | `/api/assets/:id` | Atualiza dados de um ativo | Requer JWT |
| `DELETE` | `/api/assets/:id` | Remove um ativo da carteira | Requer JWT |
| `GET` | `/api/portfolio/summary` | Retorna o resumo consolidado da carteira | Requer JWT |

---

## 🎓 Aprendizados no Desafio

- Estruturação de projetos em camadas modulares idiomáticas em Rust (`models`, `routes`, `auth`, `templates`).
- Conexão e execução assíncrona com PostgreSQL usando **SQLx**.
- Aplicação de boas práticas de segurança: hash salgado de senhas com **Argon2** e tokens **JWT**.
- Criação de middlewares e extractors no **Axum** para proteger endpoints automaticamente.
- Renderização server-side com checagem de tipos em tempo de compilação via **Askama**.
- Configuração de ambiente reproduzível com **Docker Compose** e esteira de CI no **GitHub Actions**.

---
Feito com dedicação para o desafio de Rust Fullstack! 🚀
