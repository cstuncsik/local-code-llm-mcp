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
```

### Run Code Context MCP and Ollama Locally (Recommended)

⚙️ Local Setup

**Pros:**
- Faster file access (especially on macOS)
- Easier debugging and inspection
- Persistent index stored in `~/.code-context`

**Cons:**
- Requires local installation of Ollama
- Slightly more setup steps

You run `code-context-mcp` and `ollama` locally, only Milvus runs in Docker.

1. **Install Ollama locally**

   ```bash
   curl -fsSL https://ollama.com/install.sh | sh

   # or use brew on MacOS
   brew install ollama
   ```

2. **Start Ollama and pull required models**

   Start the Ollama service and download the required models:

   ```bash
   ollama serve &
   # Or use Homebrew services to run Ollama in the background
   brew services start ollama

   ollama pull llama3
   ollama pull nomic-embed-text
   ```

   This ensures that the required models are available for embeddings.

3. **Start Milvus in Docker**

   Spin up a Milvus container only (the same `docker-compose.yml` is used):

   ```bash
   docker compose up -d milvus
   ```

4. **Add MPC server**

  ```bash
  claude mcp add local-code-mcp -e EMBEDDING_PROVIDER=Ollama -e OLLAMA_MODEL=llama3 -e EMBEDDING_MODEL=nomic-embed-text -e MILVUS_ADDRESS=localhost:19530 -e OLLAMA_HOST=http://localhost:11434 -- npx @zilliz/code-context-mcp@latest
  ```

  Or the config directly

  ```json
    {
      "mcpServers": {
        "local-code-mcp": {
          "command": "npx",
          "args": ["@zilliz/code-context-mcp@latest"],
          "env": {
            "EMBEDDING_PROVIDER": "Ollama",
            "OLLAMA_MODEL": "llama3",
            "EMBEDDING_MODEL": "nomic-embed-text",
            "MILVUS_ADDRESS": "localhost:19530",
            "OLLAMA_HOST": "http://localhost:11434"
          }
        }
      }
    }
  ```

  **Start MCP from your code directory for debugging purpose**

  ```bash
  npm install -g @zilliz/code-context-mcp@latest

  EMBEDDING_PROVIDER=Ollama \
  OLLAMA_MODEL=llama3 \
  EMBEDDING_MODEL=nomic-embed-text \
  code-context-mcp start \
    --milvus-uri localhost:19530 \
    --ollama-host http://localhost:11434
  ```

> ✅ Required environment variables for local setup (can be set in a `.env` file or exported in your shell):
>
> ```
> EMBEDDING_PROVIDER=Ollama
> OLLAMA_MODEL=llama3
> EMBEDDING_MODEL=nomic-embed-text
> MILVUS_ADDRESS=localhost:19530
> OLLAMA_HOST=http://localhost:11434
> ```
>
> These must be set when running `code-context-mcp` outside of Docker to avoid fallback to OpenAI.

> 💡 This setup avoids Docker overhead and speeds up indexing, especially on large codebases.

> ✅ Tip: If running MCP locally (not in Docker), it uses `~/.code-context` for index cache by default, so the data persists automatically between runs.

### 🪟 Windows Notes

If you're on **Windows**, the recommended approach is to use **WSL2 with Ubuntu**.

🧰 Prerequisites

  •	WSL2 with Ubuntu
  Install from Microsoft Store if not already:
  ```bash
  wsl --install -d Ubuntu
  ```

  •	Docker Desktop for Windows

    •	Enable the “Use WSL 2 based engine” in Docker settings.

    •	Allow Docker to integrate with your WSL distribution.

  •	Ollama (inside WSL2) (Ollama now supports Linux/WSL via .deb)

  ```bash
  curl -fsSL https://ollama.com/install.sh | sh
  ```

  •	Node.js inside WSL2

  ```bash
  curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
  sudo apt install -y nodejs
  ```

After installing Ollama and Node.js inside your WSL2 environment, and Docker Desktop on Windows with WSL integration enabled, all setup steps are the same as described above.

💡 *Make sure Docker is accessible from WSL and the Ollama service is started with `ollama serve &` before launching MCP.*

You can pull the models like this:

```bash
ollama pull llama3
ollama pull nomic-embed-text
```

Then run the same `docker compose up -d milvus`, `code-context-mcp start`, and `claude mcp add` commands as described.

## 🐳 Running Everything with Docker (Alternative)

🐋 Docker Setup (All-in-One)

**Pros:**
- Easy to set up with a single command
- Clean separation from host environment
- Consistent across different machines

**Cons:**
- Slower indexing due to Docker filesystem I/O
- Index is ephemeral unless volume is persisted
- Could be harder to debug inside the container

### Setup Docker Environment

```bash
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

### Persisting the Index (Optional but Recommended)

By default, when running `docker run --rm`, the container is removed after each run. This means the `.code-context` folder containing the index is lost, and the codebase will be re-indexed every time you restart the process.

To avoid this, mount a persistent volume or bind mount the `.code-context` folder from your local project:

```bash
-v "$(pwd)/.code-context":/workspace/.code-context
```

#### Full example with persistent index

```bash
# Full Claude CLI command with persistent index volume
claude mcp add local-code-mcp -s user -- \
  docker run --rm -i \
    -v .:/workspace \
    -v "$(pwd)/.code-context":/workspace/.code-context \
    -e EMBEDDING_PROVIDER=Ollama \
    -e OLLAMA_MODEL=llama3 \
    -e EMBEDDING_MODEL=nomic-embed-text \
    -e MILVUS_ADDRESS=host.docker.internal:19530 \
    -e OLLAMA_HOST=http://host.docker.internal:11434 \
    -w /workspace \
    code-context-mcp \
    code-context-mcp start --workspace /workspace
```

This will reuse the cached chunks and embeddings from `.code-context` for faster startup and better performance.

> 💡 Tip: You can safely commit `.code-context` to `.gitignore` to avoid versioning large embedding data.

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

- [@zilliz/code-context-mcp](https://github.com/zilliztech/code-context)
- [Milvus Vector DB](https://github.com/milvus-io/milvus)
- [Ollama](https://github.com/ollama/ollama)
