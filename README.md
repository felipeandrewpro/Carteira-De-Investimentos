# Carteira de Investimentos Fullstack com Rust

Aplicação web desenvolvida como evolução do desafio **Carteira de Investimentos Fullstack com Rust**, da Digital Innovation One (DIO). O projeto reúne interface web, API REST, autenticação e PostgreSQL em uma única aplicação Rust.

> Projeto educacional para organização de dados. Não fornece cotações nem recomendações financeiras.

## Funcionalidades

- Cadastro, login e logout;
- Senhas protegidas com Argon2;
- JWT em cookie HTTP-only ou `Authorization: Bearer`;
- Dashboard responsivo;
- Cadastro, listagem, edição e exclusão de ativos;
- Carteira individual por usuário;
- Validação de dados e mensagens de erro;
- Cálculo por posição: `quantidade × preço médio`;
- Cálculo do patrimônio total investido;
- API REST protegida;
- Migrations automáticas do PostgreSQL;
- Testes automatizados e integração contínua.

## Melhoria implementada

A base do desafio foi transformada em uma carteira multiusuário completa. Cada ativo possui nome, ticker, tipo, quantidade e preço médio. O dashboard calcula o total de cada posição e o patrimônio consolidado, além de oferecer formulários de criação, edição e exclusão.

## Tecnologias

- Rust 2021, Axum e Tokio;
- PostgreSQL e SQLx;
- Askama, HTML e CSS;
- JSON Web Token (`jsonwebtoken`);
- Argon2 (`password-auth`);
- Docker Compose;
- GitHub Actions.

## Estrutura

```text
.
├── .github/workflows/ci.yml
├── migrations/
├── src/
│   ├── api.rs
│   ├── app.rs
│   ├── auth.rs
│   ├── config.rs
│   ├── domain.rs
│   ├── error.rs
│   ├── lib.rs
│   ├── main.rs
│   ├── models.rs
│   ├── repository.rs
│   └── web.rs
├── static/style.css
├── templates/
├── tests/
├── .env.example
├── Cargo.toml
└── compose.yml
```

## Como executar

### Pré-requisitos

- [Rust estável](https://www.rust-lang.org/tools/install);
- Docker com Docker Compose.

### 1. Clone e configure

```bash
git clone https://github.com/felipeandrewpro/Carteira-De-Investimentos.git
cd Carteira-De-Investimentos
cp .env.example .env
```

No Windows PowerShell, use `Copy-Item .env.example .env`.

Altere `JWT_SECRET` no `.env` para uma chave aleatória com pelo menos 32 caracteres. O arquivo `.env` não deve ser enviado ao GitHub.

### 2. Inicie o PostgreSQL

```bash
docker compose up -d
```

Confira a saúde do banco:

```bash
docker compose ps
```

### 3. Inicie a aplicação

```bash
cargo run
```

As migrations são aplicadas automaticamente. Abra [http://localhost:3000](http://localhost:3000) e crie uma conta.

Para encerrar o banco:

```bash
docker compose down
```

Use `docker compose down -v` somente se também quiser apagar todos os dados locais.

## Variáveis de ambiente

| Variável | Finalidade | Padrão no exemplo |
|---|---|---|
| `DATABASE_URL` | Conexão PostgreSQL | `postgres://postgres:postgres@localhost:5432/carteira_investimentos` |
| `JWT_SECRET` | Assinatura dos tokens | Deve ter 32+ caracteres |
| `COOKIE_SECURE` | Envia cookie apenas por HTTPS | `false` no ambiente local |
| `APP_HOST` | Endereço do servidor | `127.0.0.1` |
| `APP_PORT` | Porta HTTP | `3000` |
| `RUST_LOG` | Nível de logs | `info` |

Em produção, use HTTPS e `COOKIE_SECURE=true`.

## Páginas

| Método | Rota | Função |
|---|---|---|
| `GET/POST` | `/register` | Cadastro |
| `GET/POST` | `/login` | Login |
| `POST` | `/logout` | Logout |
| `GET` | `/dashboard` | Carteira protegida |
| `GET/POST` | `/assets/new` | Novo ativo |
| `GET/POST` | `/assets/{id}/edit` | Editar ativo |
| `POST` | `/assets/{id}/delete` | Excluir ativo |

## API REST

### Cadastro

```http
POST /api/auth/register
Content-Type: application/json

{
  "username": "felipe",
  "password": "senha-segura"
}
```

### Login

```http
POST /api/auth/login
Content-Type: application/json

{
  "username": "felipe",
  "password": "senha-segura"
}
```

A resposta contém `user_id`, `username` e `token`. Envie o token nas rotas protegidas:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

### Ativos

| Método | Rota | Função |
|---|---|---|
| `GET` | `/api/assets` | Listar |
| `POST` | `/api/assets` | Criar |
| `PUT` | `/api/assets/{uuid}` | Atualizar |
| `DELETE` | `/api/assets/{uuid}` | Excluir |

Corpo para criação e atualização:

```json
{
  "name": "Petrobras PN",
  "ticker": "PETR4",
  "asset_type": "Ação",
  "quantity": 100,
  "average_price": 38.50
}
```

Verificação pública de saúde: `GET /health`.

## Testes e qualidade

```bash
cargo fmt --all -- --check
cargo check --all-targets
cargo test --all-targets
cargo clippy --all-targets -- -D warnings
```

Os testes cobrem cálculos, validações, normalização, hash de senha, assinatura JWT, prioridade de Bearer token, rota de saúde, bloqueio de acesso anônimo e renderização da página de login.

## Segurança

- Senhas nunca são salvas em texto puro;
- JWT possui expiração de 24 horas;
- Cookies usam `HttpOnly` e `SameSite=Lax`;
- Ativos são consultados e alterados com `user_id`;
- SQLx usa parâmetros em todas as consultas;
- Configurações sensíveis ficam fora do código.

## O que aprendi

- Estruturação de uma aplicação Rust em módulos;
- Rotas, estados e respostas com Axum;
- Programação assíncrona com Tokio;
- SQLx, migrations e relacionamentos no PostgreSQL;
- Autenticação JWT e hash Argon2;
- Renderização server-side com Askama;
- Validações, testes e CI.

## Projeto base

[digitalinnovationone/rust-fullstack-carteira-investimentos](https://github.com/digitalinnovationone/rust-fullstack-carteira-investimentos)

## Licença

MIT — uso educacional.
