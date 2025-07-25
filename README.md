# local-code-llm-mcp

Run local semantic code search on any codebase using Docker, Milvus, and Code Context MCP. Works with Claude Code, Gemini CLI, ChatGPT Desktop, and more.

## 🚀 Getting Started

### Prerequisites

- Docker Desktop (or Docker Engine)
- Git

### Setup

```bash
git clone git@github.com:cstuncsik/local-code-llm-mcp.git
cd local-code-llm-mcp
docker compose up --build -d
```

### Connect MCP to Claude Code (CLI)

Run this command from inside the project folder you want to index:

```bash
claude mcp add local-code-mcp -s user -- \
  docker run --rm -i \
    -v .:/workspace \
    -e EMBEDDING_PROVIDER=Ollama \
    -e OLLAMA_MODEL=llama3 \
    -e EMBEDDING_MODEL=nomic-embed-text \
    -e MILVUS_ADDRESS=host.docker.internal:19530 \
    -e OLLAMA_HOST=http://host.docker.internal:11434 \
    -w /workspace \
    code-context-mcp \
    code-context-mcp start --workspace /workspace
```

> ✅ **Tip:** When asking Claude to index the codebase, explicitly specify the path as `.`:
>
> ```
> Index this repo for semantic search with MCP local-code-mcp at path .
> ```
> This ensures the correct path is passed to the MCP container and avoids issues with mismatched host paths.

### Start using it

Ask your LLM:

```
Where are the webhook handlers defined?
What happens when a user logs in?
How is the checkout process handled?
Show me the API routes related to orders.
```

## 🧠 Features

- Local code indexing for LLM context (reduces token usage)
- AST-based code chunking
- Ollama-powered local embeddings
- Fast semantic search via Milvus

## 🔗 References

- [@zilliz/code-context-mcp](https://github.com/zilliztech/code-context-mcp)
- [Milvus Vector DB](https://github.com/milvus-io/milvus)
