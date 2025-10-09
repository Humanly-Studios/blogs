---
title: "Configuración Local de LiteLLM: Ejecuta Modelos de IA Localmente con Podman"
date: 2025-09-29T21:43:54Z
draft: false
tags: ["IA", "LiteLLM", "Podman", "Desarrollo Local", "Hugging Face", "qwen2.5-coder"]
---

# Configuración Local de LiteLLM: Ejecuta Modelos de IA Localmente con Podman

Ejecutar modelos de IA localmente te da control, privacidad y ahorro de costos. Esta guía muestra cómo configurar el proxy [LiteLLM](https://github.com/BerriAI/litellm) localmente usando contenedores [Podman](https://podman.io/) para acceder a modelos potentes como [Qwen2.5-Coder](https://endpoints.huggingface.co/catalog?query=Qwen2.5-Coder) ejecutándose en un [endpoint de Hugging Face](https://endpoints.huggingface.co/catalog).

## ¿Qué es LiteLLM?

[LiteLLM](https://github.com/BerriAI/litellm) es un servidor proxy que proporciona una interfaz API unificada para varios modelos de IA. Traduce solicitudes compatibles con OpenAI a diferentes proveedores de modelos, facilitando el cambio entre modelos sin modificar tu código.

## Arquitectura del Sistema

Así es como funciona la configuración local de LiteLLM:

```mermaid
graph TB
    subgraph "Entorno de Desarrollo Local"
        Client["Tu Aplicación<br/>(Aider, Cascade, etc.)"]
        Task["Taskfile.yaml<br/>Automatización"]
    end
    
    subgraph "Contenedor Podman"
        LiteLLM["Proxy LiteLLM<br/>:4000"]
        Config["config.yaml<br/>Configuración de Modelos"]
    end
    
    subgraph "Servicios Externos"
        HF["Hugging Face<br/>API de Modelos"]
        Models["Modelos de IA<br/>(Qwen2.5-Coder, etc.)"]
    end
    
    Client -->|"Llamadas API compatibles con OpenAI"| LiteLLM
    Task -->|"Gestión de Contenedores"| LiteLLM
    LiteLLM -->|"Cargar Configuración"| Config
    LiteLLM -->|"Solicitudes de Modelos"| HF
    HF -->|"Acceso a Modelos"| Models
    
    style Client fill:#e1f5fe
    style LiteLLM fill:#f3e5f5
    style Task fill:#e8f5e8
    style HF fill:#fff3e0
    style Models fill:#e8eaf6
    style Config fill:#fce4ec
```

## Cómo Funciona

La configuración utiliza un Taskfile.yaml que automatiza:

1. **Gestión de Contenedores**: Ejecuta LiteLLM como un contenedor Podman
2. **Configuración**: Carga modelos desde un [archivo de configuración YAML](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/config/config.yaml)
3. **Configuración del Entorno**: Gestiona claves API y [variables de entorno](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/.env)
4. **Pruebas**: Proporciona endpoints [para verificar](https://github.com/Humanly-Studios/blogs/blob/master/recipes/litellm/podman/Taskfile.yaml#L43) que todo funciona

## Inicio Rápido

### Requisitos Previos
- Podman instalado
- Clave API de Hugging Face
- [Task runner](https://taskfile.dev/) ([go-task.github.io](https://github.com/go-task/task))

### Uso Básico

El [taskfile.yaml](https://github.com/Humanly-Studios/humanly-labs/blob/main/taskfile.yaml) proporciona un flujo de trabajo simple para gestionar el contenedor LiteLLM:


```bash
task: Available tasks for this project:
* info:                     Display information about the LiteLLM setup
* install-aider:            Install Aider AI pair programming tool
* logs-litellm:             View LiteLLM container logs
* restart-litellm:          Restart the LiteLLM Podman container
* run-litellm:              Run LiteLLM as a Podman container
* setup-aider:              Configure Aider to use LiteLLM proxy with qwen2.5-coder-14b model
* setup-env:                Set up environment variables for cascade-code
* setup-env-template:       Copy environment template to .env for editing
* start-aider:              Start Aider with your configured qwen2.5-coder-14b model
* stop-litellm:             Stop the LiteLLM Podman container
* test-aider:               Test Aider integration with LiteLLM qwen2.5-coder-14b model
* test-endpoint:            Test the Hugging Face endpoint directly
* test-litellm:             Test if LiteLLM is running and accessible
* test-litellm-api:         Test LiteLLM proxy API directl
```

#### Tareas Comunes

1. **Iniciar LiteLLM**:
   ```bash
   task run-litellm
   ```

2. **Probar la configuración**:
   ```bash
   task test-litellm
   task test-qwen
   ```

3. **Ver registros**:
   ```bash
   task logs-litellm
   ```

4. **Detener cuando done**:
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
- **`test-endpoint`**: Direct [Hugging Face endpoint](https://endpoints.huggingface.co/catalog) test
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

### Sample Configuration File config.yaml

Here's the complete [`config.yaml`](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/config/config.yaml) structure used in this setup:

```yaml
model_list:
  - model_name: qwen2.5-coder-14b
    litellm_params:
      model: huggingface/qwen2.5-coder-14b
      api_base: https://gysfxmkk.us-east-1.aws.endpoints.huggingface.cloud
      api_key: os.environ/HUGGINGFACE_API_KEY
      custom_llm_provider: huggingface

general_settings:
  master_key: sk-cascade-master-key
  drop_params: true
  set_verbose: true
```

### Sample .env File

Here's the complete [`.env`](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/.env) structure used in this setup:

```env
LITELLM_PORT=5000
HUGGINGFACE_API_KEY=your_huggingface_api_key
```

**Key Configuration Elements:**
- **`model_name`**: The identifier you'll use in API requests
- **`model`**: The actual Hugging Face model path
- **`api_base`**: Your Hugging Face inference endpoint URL
- **`api_key`**: References the environment variable for security
- **`master_key`**: Authentication key for LiteLLM proxy access
- **`drop_params`**: Ignores unsupported parameters to prevent errors
- **`set_verbose`**: Enables detailed logging for debugging

## Why Use This Setup?

- **Privacy**: Your code and queries stay local
- **Cost Control**: No per-token charges for local inference
- **Flexibility**: Easy to switch between different models
- **Development**: Perfect for AI-assisted coding workflows

## Integration Examples

Once running, you can use it with any OpenAI-compatible client:

```bash
curl -X POST http://localhost:4000/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-cascade-master-key" \
  -d '{"model": "qwen2.5-coder-14b", "messages": [{"role": "user", "content": "Your prompt here"}]}'
```

Esta configuración proporciona una base sólida para el desarrollo de IA local con modelos de nivel empresarial ejecutándose en tu propia infraestructura.

---

