# desafioOpenApi

API REST desenvolvida com FastAPI para gerenciamento de contas correntes e transacoes financeiras. O projeto permite criar contas, listar contas, autenticar um usuario por JWT e registrar operacoes de deposito e saque, atualizando o saldo da conta de forma transacional.

## Tecnologias

- Python 3.12+
- FastAPI
- Uvicorn
- Pydantic
- SQLAlchemy
- Databases
- PyJWT
- Poetry

## Funcionalidades

- Autenticacao com token JWT.
- Criacao e listagem de contas.
- Registro de transacoes do tipo `deposit` e `withdrawal`.
- Consulta de transacoes por conta.
- Validacao de saldo antes de realizar saques.
- Tratamento de erros para conta inexistente e regras de negocio.

## Estrutura do projeto

```text
src/
  controllers/  # Rotas da API
  models/       # Definicao das tabelas
  schemas/      # Dados de entrada
  services/     # Regras de negocio
  views/        # Dados de resposta
  config.py     # Configuracoes por variaveis de ambiente
  database.py   # Conexao com o banco de dados
  main.py       # Aplicacao FastAPI
  security.py   # Geracao e validacao de JWT
```

## Configuracao

Crie um arquivo `.env` na raiz do projeto com a URL do banco de dados:

```env
DATABASE_URL=sqlite:///./database.db
ENVIRONMENT=development
```

Para PostgreSQL, use uma URL no formato:

```env
DATABASE_URL=postgresql://usuario:senha@localhost:5432/nome_do_banco
ENVIRONMENT=production
```

## Instalacao

Instale as dependencias com Poetry:

```bash
poetry install
```

## Executando a API

Inicie o servidor local:

```bash
poetry run uvicorn src.main:app --reload
```

A documentacao interativa da API fica disponivel em:

```text
http://localhost:8000/docs
```

## Autenticacao

Para acessar as rotas protegidas, primeiro gere um token:

```http
POST /auth/login
```

Exemplo de corpo da requisicao:

```json
{
  "user_id": 1
}
```

Use o token retornado no header das proximas requisicoes:

```http
Authorization: Bearer <token>
```

## Rotas principais

| Metodo | Rota | Descricao |
| --- | --- | --- |
| `POST` | `/auth/login` | Gera um token JWT para o usuario informado. |
| `GET` | `/accounts/?limit=10&skip=0` | Lista contas cadastradas. |
| `POST` | `/accounts/` | Cria uma conta. |
| `GET` | `/accounts/{id}/transactions?limit=10&skip=0` | Lista transacoes de uma conta. |
| `POST` | `/transactions/` | Cria uma transacao de deposito ou saque. |

## Exemplos de payload

Criar conta:

```json
{
  "user_id": 1,
  "balance": 100.0
}
```

Criar deposito:

```json
{
  "account_id": 1,
  "type": "deposit",
  "amount": 50.0
}
```

Criar saque:

```json
{
  "account_id": 1,
  "type": "withdrawal",
  "amount": 25.0
}
```

## Regras de negocio

- O valor inicial da conta deve ser positivo.
- O valor da transacao deve ser positivo.
- Saques so sao realizados quando a conta possui saldo suficiente.
- Transacoes so podem ser criadas para contas existentes.
