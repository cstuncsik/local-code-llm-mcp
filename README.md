# local-code-llm-mcp

Local semantic code search for any codebase using Docker, Milvus, and MCP — integrates with Claude Code, Gemini CLI, ChatGPT Desktop, and others.

## 🚀 Getting Started

### 1. Prerequisites

- Docker Desktop (or Docker Engine)
- Git
- [Ollama](https://ollama.com/) installed locally
- An embedding model (e.g. `jina-embeddings-v2-base-code`) pulled into Ollama

### 2. Clone and setup

```bash
git clone https://github.com/yourusername/local-code-llm-mcp.git
cd local-code-llm-mcp
```

### 3. Install Ollama and pull a model

#### On macOS:
```bash
brew install ollama
ollama pull unclemusclez/jina-embeddings-v2-base-code
```

#### On Linux:
Follow instructions at: https://ollama.com/download

#### On Windows (WSL2 required):
1. Install WSL2 and a Linux distribution (Ubuntu recommended)
2. Inside WSL:
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ollama pull unclemusclez/jina-embeddings-v2-base-code
   ```

### 4. Start Milvus and the MCP server

```bash
docker-compose up --build -d
```

### 5. Configure your MCP client (e.g., Claude CLI, ChatGPT Desktop)

Add this to your MCP config:
```json
{
  "command": "docker",
  "args": [
    "exec", "-i", "code-context-mcp",
    "code-context-mcp",
    "--milvus-uri", "http://host.docker.internal:19530"
  ],
  "env": { "EMBEDDING_PROVIDER": "ollama" }
}
```

### 6. Interact

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

- Fully offline
- Uses AST-based chunking
- Ollama-powered local embeddings
- Fast semantic search via Milvus
