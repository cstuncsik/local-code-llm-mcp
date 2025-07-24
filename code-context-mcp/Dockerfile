FROM node:18-slim

WORKDIR /app

RUN npm install -g @zilliz/code-context-mcp

EXPOSE 4444

CMD ["code-context-mcp", "--milvus-uri", "http://host.docker.internal:19530"\]
