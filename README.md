# StockBot

Sistema web de controle de estoque. Cada conta gerencia os próprios produtos, categorias, fornecedores e movimentações, com painel, alertas de estoque baixo e relatórios.

## Funcionalidades

- Cadastro, login e perfil (nome, e-mail, senha e exclusão de conta)
- Produtos, categorias e fornecedores isolados por usuário
- Entrada e saída de estoque em transação atômica (saída recusa quantidade maior que o disponível)
- Painel com valor em estoque, status e gráficos
- Relatórios filtráveis por período, categoria e tipo de movimentação
- Alertas de estoque zerado ou abaixo do mínimo (preferências em Configurações)
- Página de contato e catálogo interno de componentes (`/modelo/`)

## Stack

| Camada | Tecnologia |
| --- | --- |
| Backend | Django 5.2, Python 3.11 |
| Banco | SQLite (padrão) ou PostgreSQL via `DATABASE_URL` |
| Templates | Django Templates + Crispy Forms (Tailwind) |
| CSS | Tailwind CSS 4 |
| Servidor | Gunicorn + WhiteNoise |
| Container | Docker / Docker Compose |
| CI | GitHub Actions (`python manage.py test website`) |

Arquitetura detalhada: [SYSTEM_DESIGN.md](SYSTEM_DESIGN.md).

## Pré-requisitos

- Python 3.11+
- Node.js (só se for recompilar o CSS)
- Docker (opcional)

## Como rodar localmente

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux/macOS: source .venv/bin/activate
pip install -r requirements.txt
```

Crie um arquivo `.env` na raiz:

```env
SECRET_KEY=troque-esta-chave
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
```

Para PostgreSQL (Neon ou outro), acrescente:

```env
DATABASE_URL=postgres://usuario:senha@host:5432/banco
```

Depois:

```bash
python manage.py migrate
python manage.py runserver
```

Acesse [http://127.0.0.1:8000](http://127.0.0.1:8000).

### CSS (Tailwind)

O CSS compilado já está em `static/css/style.css`. Para alterar estilos:

```bash
npm install
npm run watch:css
```

Build único: `npm run build:css`.

## Docker

```bash
docker compose up --build
```

A aplicação sobe em `http://localhost:8000` com o `.env` montado no container.

## Testes

```bash
python manage.py test website --verbosity=2
```

O CI em `.github/workflows/ci.yml` executa a mesma suíte em todo push e pull request em `main`.

## Estrutura

```
Estoque/          # Projeto Django (settings, urls, WSGI)
website/          # App principal (models, views, templates, testes)
static/           # CSS, imagens e favicon
.github/workflows # CI
Dockerfile
docker-compose.yml
requirements.txt
```

## Variáveis de ambiente

| Variável | Obrigatória | Padrão | Uso |
| --- | --- | --- | --- |
| `SECRET_KEY` | sim | — | Assinatura do Django |
| `DEBUG` | não | `False` | Modo desenvolvimento |
| `ALLOWED_HOSTS` | não | `*` | Hosts aceitos |
| `DATABASE_URL` | não | SQLite em `db.sqlite3` | Banco |
| `SECURE_SSL_REDIRECT` | não | `True` se `DEBUG=False` | Redirect HTTPS |
| `SESSION_COOKIE_SECURE` | não | `True` se `DEBUG=False` | Cookie de sessão só em HTTPS |
| `CSRF_COOKIE_SECURE` | não | `True` se `DEBUG=False` | Cookie CSRF só em HTTPS |
| `SECURE_HSTS_SECONDS` | não | `31536000` | HSTS em produção |

Não commite o `.env`. O arquivo já está no `.gitignore`.
