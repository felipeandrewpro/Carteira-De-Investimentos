# Carteira de Investimentos Fullstack com Rust

Aplicação Fullstack desenvolvida como evolução do desafio **Carteira de Investimentos Fullstack com Rust**, da Digital Innovation One (DIO).

O projeto conecta **Back-End, banco de dados, autenticação e interface web** em uma única aplicação.

## Funcionalidades

- Cadastro de usuários;
- Login e logout;
- Senhas armazenadas com hash;
- Autenticação com JWT;
- Sessão autenticada por cookie HTTP-only;
- Suporte a JWT pelo header `Authorization: Bearer`;
- Cadastro de ativos;
- Listagem de ativos;
- Edição de ativos;
- Exclusão de ativos;
- Validações de dados;
- Quantidade por ativo;
- Valor unitário;
- Cálculo automático do valor da posição;
- Cálculo do patrimônio total da carteira;
- Dashboard responsivo;
- API REST;
- Migrations automáticas do PostgreSQL;
- Banco PostgreSQL via Docker Compose;
- Testes unitários de regras de validação.

## Melhorias implementadas

A versão foi evoluída para transformar a listagem simples de ativos em uma carteira mais completa.

Cada posição agora possui:

- Nome;
- Ticker;
- Tipo do ativo;
- Quantidade;
- Valor unitário;
- Valor total da posição.

O dashboard calcula automaticamente:

```text
valor_da_posicao = quantidade × valor_unitario
```

e:

```text
patrimonio_total = soma_de_todas_as_posicoes
```

Também foram adicionados:

- Cadastro separado de usuários;
- Dashboard profissional;
- Formulários de criação e edição;
- Exclusão de ativos;
- Validação de entradas;
- Segredo JWT por variável de ambiente;
- Cookie HTTP-only;
- API REST protegida;
- README de execução e documentação.

## Tecnologias

- Rust
- Axum
- Tokio
- PostgreSQL
- SQLx
- Askama
- JWT (`jwt-simple`)
- `password-auth`
- Docker Compose
- HTML
- CSS

## Estrutura

```text
Carteira-De-Investimentos/
├── .github/
│   └── workflows/
│       └── ci.yml
├── migrations/
│   ├── 20260828000100_create_users.down.sql
│   ├── 20260828000100_create_users.up.sql
│   ├── 20260828000200_create_assets.down.sql
│   └── 20260828000200_create_assets.up.sql
├── src/
│   ├── auth/
│   │   ├── mod.rs
│   │   └── user.rs
│   ├── routes/
│   │   ├── api.rs
│   │   ├── frontend.rs
│   │   └── mod.rs
│   ├── app.rs
│   ├── error.rs
│   ├── main.rs
│   ├── models.rs
│   └── repository.rs
├── static/
│   └── style.css
├── templates/
│   ├── asset_form.html
│   ├── dashboard.html
│   ├── login.html
│   └── register.html
├── .env.example
├── .gitignore
├── Cargo.toml
├── compose.yml
└── README.md
```

## Pré-requisitos

Tenha instalado:

- Rust com Cargo;
- Docker;
- Docker Compose.

Como o projeto usa a edição 2024 do Rust, utilize uma versão recente da toolchain estável.

## Como executar

### 1. Clone o repositório

```bash
git clone https://github.com/felipeandrewpro/Carteira-De-Investimentos.git
cd Carteira-De-Investimentos
```

### 2. Configure as variáveis de ambiente

Linux/macOS:

```bash
cp .env.example .env
```

Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Edite o arquivo `.env` e altere `JWT_SECRET` por uma chave longa.

Exemplo:

```env
DATABASE_URL=postgres://postgres:postgres@localhost:5432/carteira_investimentos
JWT_SECRET=uma-chave-local-grande-e-segura-com-mais-de-24-caracteres
COOKIE_SECURE=false
```

> Não envie o arquivo `.env` para o GitHub. Ele já está listado no `.gitignore`.

### 3. Inicie o PostgreSQL

```bash
docker compose up -d
```

### 4. Execute a aplicação

```bash
cargo run
```

As migrations são executadas automaticamente quando a aplicação inicia.

### 5. Abra no navegador

```text
http://localhost:3000
```

Crie uma conta em:

```text
http://localhost:3000/register
```

## API REST

A API utiliza a mesma autenticação JWT da aplicação web.

### Criar usuário pela API

```http
POST /api/auth/register
Content-Type: application/json
```

```json
{
  "username": "usuario",
  "password": "senha123"
}
```

A resposta contém um token JWT.

### Login pela API

```http
POST /api/auth/login
Content-Type: application/json
```

```json
{
  "username": "usuario",
  "password": "senha123"
}
```

Exemplo de resposta:

```json
{
  "user_id": 1,
  "username": "usuario",
  "token": "SEU_TOKEN_JWT"
}
```

Use esse token nas rotas protegidas:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

### Listar ativos

```http
GET /api/assets
```

### Criar ativo

```http
POST /api/assets
Content-Type: application/json
```

Exemplo:

```json
{
  "name": "Petrobras PN",
  "ticker": "PETR4",
  "asset_type": "Ação",
  "quantity": 100,
  "unit_value": 38.5
}
```

### Atualizar ativo

```http
PUT /api/assets/1
Content-Type: application/json
```

### Excluir ativo

```http
DELETE /api/assets/1
```

### Resumo da carteira

```http
GET /api/portfolio/summary
```

Resposta:

```json
{
  "asset_count": 3,
  "total_value": 18450.0
}
```

## Autenticação da API

As rotas de ativos aceitam o cookie criado pelo login web ou o token retornado
por `/api/auth/login`:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

## Testes

Execute:

```bash
cargo test
```

Para conferir a compilação:

```bash
cargo check
```

Para validar a formatação:

```bash
cargo fmt --all --check
```

## Segurança

Este é um projeto educacional, mas algumas práticas importantes foram aplicadas:

- Hash de senhas;
- JWT assinado;
- Segredo JWT fora do código;
- Cookie HTTP-only;
- `SameSite=Lax`;
- Validação de propriedade dos ativos por usuário;
- Queries parametrizadas com SQLx;
- `.env` ignorado pelo Git.

Em produção, também devem ser considerados HTTPS obrigatório, `COOKIE_SECURE=true`, proteção CSRF, política de expiração/renovação de token e gestão segura de segredos.

## O que aprendi

Durante o desenvolvimento foram praticados:

- Organização de projeto Rust;
- Rotas e extractors do Axum;
- Programação assíncrona com Tokio;
- Persistência com SQLx;
- Migrations;
- Relacionamento entre usuários e ativos;
- Autenticação e autorização;
- JWT e cookies;
- Hash de senhas;
- Templates server-side com Askama;
- API REST;
- Tratamento de erros;
- Validação de regras de negócio;
- Docker e PostgreSQL;
- Testes em Rust.

## Projeto base

Projeto inspirado e evoluído a partir do repositório educacional da DIO:

`digitalinnovationone/rust-fullstack-carteira-investimentos`

## Licença

Projeto disponibilizado para fins educacionais.
