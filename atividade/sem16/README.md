# IA Vector Database — PostgreSQL + pgvector

## 📌 Objetivo

Este repositório contém uma infraestrutura Plug & Play de banco de dados vetorizado utilizando PostgreSQL + pgvector com Docker Compose.

O objetivo é permitir que qualquer aplicação de IA, RAG (Retrieval-Augmented Generation) ou busca semântica consiga subir rapidamente um banco preparado para armazenar embeddings.

---

# 🚀 Tecnologias Utilizadas

- Docker
- Docker Compose
- PostgreSQL 16
- pgvector

---

# 📦 Estrutura do Projeto

```txt
.
├── docker-compose.yml
├── init.sql
└── README.md
```

---

# ▶️ Como Subir a Infraestrutura

Execute o comando:

```bash
docker-compose up -d
```

---

# 🛑 Como Destruir a Infraestrutura

Execute o comando:

```bash
docker-compose down -v
```

O parâmetro `-v` remove os volumes e limpa completamente os dados persistidos.

---

# 🔑 Credenciais Padrão

| Variável | Valor |
|---|---|
| POSTGRES_USER | admin |
| POSTGRES_PASSWORD | admin123 |
| POSTGRES_DB | vector_db |

---

# 🧠 Extensão pgvector

A extensão `vector` é instalada automaticamente através do arquivo `init.sql`.

Verificar extensões instaladas:

```bash
docker exec -it ia-vector-db psql -U admin -d vector_db -c "\dx"
```

---

# 📂 Persistência de Dados

Os dados do PostgreSQL são persistidos através do volume:

```yaml
postgres_data:/var/lib/postgresql/data
```

Isso garante que os dados não sejam perdidos após reinicialização dos containers.

---

# ⚠️ Troubleshooting

## Porta 5432 já está em uso

Altere no `docker-compose.yml`:

```yaml
ports:
  - "5433:5432"
```

---

## Extensão vector não encontrada

Verifique se a imagem utilizada é:

```yaml
pgvector/pgvector:pg16
```

---

## init.sql não executa

Execute:

```bash
docker-compose down -v
docker-compose up -d
```

---

# 📚 Conceitos Arquiteturais Aplicados

- Cloud Native
- IaaS com Docker
- Persistência via Volumes
- Infraestrutura Declarativa
- 12-Factor App
- Banco Vetorizado para IA
- Embeddings e RAG

---

# 👩‍💻 Autor

Projeto acadêmico desenvolvido para a disciplina de Arquitetura em Nuvem.
