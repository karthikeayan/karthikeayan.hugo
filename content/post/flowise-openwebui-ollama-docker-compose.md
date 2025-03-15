---
title: flowise-openwebui-ollama-docker-compose
description: Docker Compose for Flowise OpenWebUI Ollama
tags: ["ai", "docker-compose", "flowise", "ollama", "openwebui", "local", "localai"]
date: 2025-15-03
publishdate: 2025-15-03
---

#  Docker Compose for Flowise OpenWebUI Ollama

Create .env file as below,
```
FLOWISE_USERNAME:admin
FLOWISSE_PASSWORD:admin
```

```
services:
  flowise:
    image: flowiseai/flowise
    restart: always
    environment:
      - PORT=3001
      - FLOWISE_USERNAME=${FLOWISE_USERNAME}
      - FLOWISE_PASSWORD=${FLOWISE_PASSWORD}
    ports:
      - "3001:3001"
    volumes:
      - ./.flowise:/root/.flowise
    entrypoint: /bin/sh -c "sleep 3; flowise start"

  openWebUI:
    image: ghcr.io/open-webui/open-webui:main
    restart: always
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_API_BASE_URL=http://ollama:11434
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - ./open-webui-local:/app/backend/data
    depends_on:
      - ollama

  ollama:
    image: ollama/ollama
    environment:
      - 'OLLAMA_HOST=0.0.0.0'
      - 'OLLAMA_PORT=11434'
    ports:
      - "11434:11434"
    volumes:
      - ./ollama-local:/root/.ollama
```
