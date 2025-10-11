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

El [taskfile.yaml](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/Taskfile.yaml) proporciona un flujo de trabajo simple para gestionar el contenedor LiteLLM:


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

## Flujo de Solicitudes

Así es como fluyen las solicitudes a través del proxy de LiteLLM:

```mermaid
sequenceDiagram
    participant App as Tu Aplicación
    participant Proxy as Proxy LiteLLM
    participant Config as Archivo de Config
    participant HF as API Hugging Face
    participant Model as Modelo IA
    
    App->>Proxy: POST /chat/completions<br/>{model: "qwen2.5-coder-14b"}
    Proxy->>Config: Cargar configuración del modelo
    Config-->>Proxy: Endpoint y configuraciones del modelo
    Proxy->>HF: Transformar solicitud a formato HF
    HF->>Model: Ejecutar inferencia
    Model-->>HF: Respuesta del modelo
    HF-->>Proxy: Respuesta de la API
    Proxy->>Proxy: Transformar a formato OpenAI
    Proxy-->>App: Respuesta compatible con OpenAI
    
    Note over App,Model: Toda la comunicación permanece local<br/>excepto la inferencia del modelo
```

## Key Features

### Gestión de Contenedores
- **`run-litellm`**: Inicia el proxy en el puerto 4000
- **`stop-litellm`**: Detiene y elimina el contenedor de forma limpia
- **`restart-litellm`**: Reinicio rápido para cambios de configuración

### Pruebas y Monitoreo
- **`test-litellm`**: Endpoint de verificación de salud
- **`test-qwen`**: Prueba específicamente el modelo Qwen2.5-Coder
- **`test-endpoint`**: Prueba directa del [endpoint de Hugging Face](https://endpoints.huggingface.co/catalog)
- **`logs-litellm`**: Logs del contenedor en tiempo real

### Integración de Desarrollo
- **`setup-env`**: Crea archivo `.env` para integración con cascade-code
- **`setup-aider`**: Configura la herramienta de programación en pareja Aider AI
- **`install-aider`**: Instala Aider para codificación asistida por IA

## Configuración

La configuración requiere:
- Archivo de configuración en `config/config.yaml`
- Variables de entorno en archivo `.env`
- Clave API de Hugging Face para acceso a modelos

### Archivo de Configuración de Ejemplo config.yaml

Aquí está la estructura completa de [`config.yaml`](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/config/config.yaml) utilizada en esta configuración:

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

### Archivo .env de Ejemplo

Aquí está la estructura completa del [`.env`](https://github.com/Humanly-Studios/blogs/blob/main/recipes/litellm/podman/.env) utilizada en esta configuración:

```env
LITELLM_PORT=5000
HUGGINGFACE_API_KEY=tu_clave_api_huggingface
```

**Elementos Clave de Configuración:**
- **`model_name`**: El identificador que usarás en las solicitudes de API
- **`model`**: La ruta real del modelo de Hugging Face
- **`api_base`**: La URL de tu endpoint de inferencia de Hugging Face
- **`api_key`**: Hace referencia a la variable de entorno para seguridad
- **`master_key`**: Clave de autenticación para el acceso al proxy de LiteLLM
- **`drop_params`**: Ignora parámetros no soportados para prevenir errores
- **`set_verbose`**: Habilita el registro detallado para depuración

## ¿Por Qué Usar Esta Configuración?

- **Privacidad**: Tu código y consultas se mantienen locales
- **Control de Costos**: Sin cargos por token para inferencia local
- **Flexibilidad**: Fácil cambio entre diferentes modelos
- **Desarrollo**: Perfecto para flujos de trabajo de codificación asistida por IA

## Ejemplos de Integración

Una vez en funcionamiento, puedes usarlo con cualquier cliente compatible con OpenAI:

```bash
curl -X POST http://localhost:4000/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sk-cascade-master-key" \
  -d '{"model": "qwen2.5-coder-14b", "messages": [{"role": "user", "content": "Your prompt here"}]}'
```

Esta configuración proporciona una base sólida para el desarrollo de IA local con modelos de nivel empresarial ejecutándose en tu propia infraestructura.

# Repositorio Github

El codigo usado en este blog lo pueden encontrar en el repositorio [Humanly-Studios/blogs/litellms](https://github.com/Humanly-Studios/blogs/tree/main/recipes/litellm).


---

