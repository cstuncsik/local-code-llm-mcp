# local-code-llm-mcp

Local semantic code search for any codebase using Docker, Milvus, and MCP — integrates with Claude Code, Gemini CLI, ChatGPT Desktop, and others.

## 🚀 Getting Started

### 1. Prerequisites

- Docker Desktop (or Docker Engine)
- Git

### 2. Clone and setup

```bash
git clone https://github.com/yourusername/local-code-llm-mcp.git
cd local-code-llm-mcp
```

### 3. Start the stack

```bash
docker compose up --build -d
```

### 4. Add MCP to your client

#### Add MCP to Claude Code (CLI)

If you're using Claude Code in the terminal, run this command to add the local MCP:

```bash
claude mcp add local-code-mcp \
  -s user \
  "docker exec -i code-context-mcp code-context-mcp --milvus-uri http://host.docker.internal:19530"
```

### 5. Interact

Ask your LLM:
```
Please index this repo for semantic search
```

or:

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

- Locally indexed code context (reduces token usage with external LLMs)
- Uses AST-based chunking
- Ollama-powered local embeddings
- Fast semantic search via Milvus


## 🔗 References

- [@zilliz/code-context-mcp](https://github.com/zilliztech/code-context-mcp)
- [Milvus Vector DB](https://github.com/milvus-io/milvus)
