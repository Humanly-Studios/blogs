---
title: "LiteLLM Local Setup: Run AI Models Locally with Podman"
date: 2025-09-29T21:43:54Z
draft: false
tags: ["AI", "LiteLLM", "Podman", "Local Development", "Hugging Face", "qwen2.5-coder"]
---

# LiteLLM Local Setup: Run AI Models Locally with Podman

Running AI models locally gives you control, privacy, and cost savings. This guide shows how to set up LiteLLM proxy locally using Podman containers to access powerful models like Qwen2.5-Coder.

## What is LiteLLM?

LiteLLM is a proxy server that provides a unified API interface for various AI models. It translates OpenAI-compatible requests to different model providers, making it easy to switch between models without changing your code.

## System Architecture

Here's how the LiteLLM local setup works:

```mermaid
graph TB
    subgraph "Local Development Environment"
        Client["Your Application<br/>(Aider, Cascade, etc.)"]
        Task["Taskfile.yaml<br/>Automation"]
    end
    
    subgraph "Podman Container"
        LiteLLM["LiteLLM Proxy<br/>:4000"]
        Config["config.yaml<br/>Model Configuration"]
    end
    
    subgraph "External Services"
        HF["Hugging Face<br/>Model API"]
        Models["AI Models<br/>(Qwen2.5-Coder, etc.)"]
    end
    
    Client -->|"OpenAI-compatible<br/>API calls"| LiteLLM
    Task -->|"Container<br/>Management"| LiteLLM
    LiteLLM -->|"Load Config"| Config
    LiteLLM -->|"Model Requests"| HF
    HF -->|"Access Models"| Models
    
    style Client fill:#e1f5fe
    style LiteLLM fill:#f3e5f5
    style Task fill:#e8f5e8
    style HF fill:#fff3e0
```

## How It Works

The setup uses a Taskfile.yaml that automates:

1. **Container Management**: Runs LiteLLM as a Podman container
2. **Configuration**: Loads models from a YAML config file
3. **Environment Setup**: Manages API keys and environment variables
4. **Testing**: Provides endpoints to verify everything works

## Quick Start

### Prerequisites
- Podman installed
- Hugging Face API key
- Task runner (go-task.github.io)

### Basic Usage

```mermaid
flowchart TD
    Start(["Start"])
    RunLLM["task run-litellm<br/>🚀 Start Container"]
    TestHealth["task test-litellm<br/>🏥 Health Check"]
    TestModel["task test-qwen<br/>🤖 Test Model"]
    ViewLogs["task logs-litellm<br/>📋 Monitor Logs"]
    UseAPI["Use OpenAI API<br/>💻 Develop"]
    Stop["task stop-litellm<br/>🛑 Stop Container"]
    End(["End"])
    
    Start --> RunLLM
    RunLLM --> TestHealth
    TestHealth --> TestModel
    TestModel --> ViewLogs
    ViewLogs --> UseAPI
    UseAPI --> Stop
    Stop --> End
    
    ViewLogs -.->|"Monitor during use"| UseAPI
    
    style Start fill:#c8e6c9
    style End fill:#ffcdd2
    style RunLLM fill:#e1f5fe
    style UseAPI fill:#f3e5f5
```

1. **Start LiteLLM**:
   ```bash
   task run-litellm
   ```

2. **Test the setup**:
   ```bash
   task test-litellm
   task test-qwen
   ```

3. **View logs**:
   ```bash
   task logs-litellm
   ```

4. **Stop when done**:
   ```bash
   task stop-litellm
   ```

## Request Flow

Here's how requests flow through the LiteLLM proxy:

```mermaid
sequenceDiagram
    participant App as Your Application
    participant Proxy as LiteLLM Proxy
    participant Config as Config File
    participant HF as Hugging Face API
    participant Model as AI Model
    
    App->>Proxy: POST /chat/completions<br/>{model: "qwen2.5-coder-14b"}
    Proxy->>Config: Load model configuration
    Config-->>Proxy: Model endpoint & settings
    Proxy->>HF: Transform request to HF format
    HF->>Model: Execute inference
    Model-->>HF: Model response
    HF-->>Proxy: API response
    Proxy->>Proxy: Transform to OpenAI format
    Proxy-->>App: OpenAI-compatible response
    
    Note over App,Model: All communication stays local<br/>except model inference
```

## Key Features

### Container Management
- **`run-litellm`**: Starts the proxy on port 4000
- **`stop-litellm`**: Cleanly stops and removes container
- **`restart-litellm`**: Quick restart for config changes

### Testing & Monitoring
- **`test-litellm`**: Health check endpoint
- **`test-qwen`**: Tests the Qwen2.5-Coder model specifically
- **`test-endpoint`**: Direct Hugging Face endpoint test
- **`logs-litellm`**: Real-time container logs

### Development Integration
- **`setup-env`**: Creates `.env` file for cascade-code integration
- **`setup-aider`**: Configures Aider AI pair programming tool
- **`install-aider`**: Installs Aider for AI-assisted coding

## Configuration

The setup expects:
- Config file at `config/config.yaml`
- Environment variables in `.env` file
- Hugging Face API key for model access

## Why Use This Setup?

**Privacy**: Your code and queries stay local
**Cost Control**: No per-token charges for local inference
**Flexibility**: Easy to switch between different models
**Development**: Perfect for AI-assisted coding workflows

## Integration Examples

Once running, you can use it with any OpenAI-compatible client:

```bash
curl -X POST http://localhost:4000/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-cascade-master-key" \
  -d '{"model": "qwen2.5-coder-14b", "messages": [{"role": "user", "content": "Your prompt here"}]}'
```

This setup provides a robust foundation for local AI development with enterprise-grade models running on your own infrastructure.
