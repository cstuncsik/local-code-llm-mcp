FROM node:20-slim

# Install Python and build tools
RUN apt-get update && \
    apt-get install -y python3 make g++ && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN npm install -g @zilliz/code-context-mcp@latest
