# local-code-llm-mcp

Local semantic code search for any codebase using Docker, Milvus, and MCP — integrates with Claude Code, Gemini CLI, ChatGPT Desktop, and others.

## 🚀 Getting Started

1. Clone this repo
2. Install [Ollama](https://ollama.com/) and pull a local embedding model:

   ```bash
   brew install ollama
   ollama pull unclemusclez/jina-embeddings-v2-base-code
   ```

3. Start Milvus and the MCP server:

   ```bash
   docker-compose up --build -d
   ```

4. In your MCP-capable CLI (e.g., Claude):

   - Add this to your MCP config:
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

5. Ask:
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
