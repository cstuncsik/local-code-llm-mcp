# local-code-llm-mcp

Run local semantic code search on any codebase using Docker, Milvus, and Code Context MCP. Works with Claude Code, Gemini CLI, ChatGPT Desktop, and more.

## 🚀 Getting Started

### Prerequisites

- Docker Desktop (or Docker Engine)
- Git

### Setup

```bash
git clone https://github.com/yourusername/local-code-llm-mcp.git
cd local-code-llm-mcp
docker compose up --build -d
```

### Connect MCP to Claude Code (CLI)

Run this command from inside the project folder you want to index:

To connect a local MCP instance that runs in a Docker container and works in any project folder, run:

```bash
claude mcp add local-code-mcp -s user -- \
  docker run --rm -i \
    -v "$PWD":/workspace \
    -e EMBEDDING_PROVIDER=Ollama \
    -e OLLAMA_MODEL=llama3 \
    -e EMBEDDING_MODEL=nomic-embed-text \
    -e MILVUS_ADDRESS=host.docker.internal:19530 \
    -e OLLAMA_HOST=http://host.docker.internal:11434 \
    -w /workspace \
    code-context-mcp \
    code-context-mcp start --transport stdio --workspace /workspace
```

### Start using it

Ask your LLM:

```
Please index this repo for semantic search with MCP
```

Or:

```
Where are the webhook handlers defined?
```

## 🔧 Project Structure

```
local-code-llm-mcp/
├── docker-compose.yml
├── .env.example
├── README.md
└── code-context-mcp/
    └── Dockerfile
```

## 🧠 Features

- Local code indexing for LLM context (reduces token usage)
- AST-based code chunking
- Ollama-powered local embeddings
- Fast semantic search via Milvus

## 🔗 References

- [@zilliz/code-context-mcp](https://github.com/zilliztech/code-context-mcp)
- [Milvus Vector DB](https://github.com/milvus-io/milvus)
