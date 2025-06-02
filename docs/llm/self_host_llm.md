# **Docker Deployment for Qwen 3 MoE with vLLM, LiteLLM & Langfuse**

Here's a complete Docker-based setup for deploying Qwen 3 MoE with vLLM, LiteLLM for routing, and Langfuse for observability.

## **1. Directory Structure**
```
qwen-moe-deployment/
├── docker-compose.yml
├── litellm/
│   ├── config.yaml
│   └── custom_router.py (optional)
├── prometheus/
│   └── prometheus.yml (optional)
└── .env
```

## **2. docker-compose.yml**
```yaml
version: "3.8"

services:
  # vLLM OpenAI-Compatible Server
  vllm:
    image: vllm/vllm:latest
    runtime: nvidia  # Requires NVIDIA Container Toolkit
    environment:
      - HF_TOKEN=${HF_TOKEN}  # For gated models
    command: >
      --model Qwen/Qwen1.5-MoE-A2.7B
      --host 0.0.0.0
      --port 8000
      --tensor-parallel-size ${GPU_COUNT:-1}
      --gpu-memory-utilization 0.9
      --enforce-eager
      --disable-custom-all-reduce
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              capabilities: [gpu]
    ports:
      - "8000:8000"
    volumes:
      - ${MODEL_CACHE:-./models}:/root/.cache/huggingface

  # LiteLLM Proxy for Routing
  litellm:
    image: ghcr.io/berriai/litellm:main
    depends_on:
      - vllm
    ports:
      - "4000:4000"
    volumes:
      - ./litellm/config.yaml:/app/config.yaml
      - ./litellm/custom_router.py:/app/custom_router.py  # Optional
    environment:
      - LITELLM_CONFIG=/app/config.yaml

  # Langfuse for Observability
  langfuse:
    image: langfuse/langfuse:latest
    environment:
      - LANGFUSE_SECRET_KEY=${LANGFUSE_SECRET_KEY}
      - LANGFUSE_PUBLIC_KEY=${LANGFUSE_PUBLIC_KEY}
      - DATABASE_URL=postgresql://postgres:postgres@postgres:5432/langfuse
      - NEXTAUTH_SECRET=${NEXTAUTH_SECRET}
    ports:
      - "3000:3000"
    depends_on:
      - postgres

  # Postgres for Langfuse
  postgres:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_DB=langfuse
    volumes:
      - postgres_data:/var/lib/postgresql/data

  # Optional: Prometheus Monitoring
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    depends_on:
      - vllm

volumes:
  postgres_data:
  model_cache:
```

## **3. LiteLLM Configuration (litellm/config.yaml)**
```yaml
model_list:
  - model_name: qwen-moe
    litellm_params:
      model: openai/Qwen1.5-MoE-A2.7B
      api_base: http://vllm:8000/v1
      api_key: "any-string"

  # Example fallback to OpenAI
  - model_name: gpt-4-fallback
    litellm_params:
      model: gpt-4
      api_key: ${OPENAI_API_KEY}

litellm_settings:
  drop_params: True
  set_verbose: True
  success_callback: ["langfuse"]  # Enable Langfuse logging

environment_variables:
  LANGFUSE_PUBLIC_KEY: ${LANGFUSE_PUBLIC_KEY}
  LANGFUSE_SECRET_KEY: ${LANGFUSE_SECRET_KEY}
```

## **4. Environment File (.env)**
```ini
# Hugging Face (for gated models)
HF_TOKEN=hf_xxxxxxxxxxxx

# Langfuse
LANGFUSE_PUBLIC_KEY=pk-lf-xxxx
LANGFUSE_SECRET_KEY=sk-lf-xxxx
NEXTAUTH_SECRET=your-strong-secret-here

# Optional: OpenAI fallback
OPENAI_API_KEY=sk-xxxx

# GPU Configuration
GPU_COUNT=1  # Set to number of GPUs
MODEL_CACHE=./models  # Local cache directory
```

## **5. Optional: Prometheus Config (prometheus/prometheus.yml)**
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "vllm"
    static_configs:
      - targets: ["vllm:8000"]
  - job_name: "litellm"
    static_configs:
      - targets: ["litellm:4000"]
```

## **6. Deployment Commands**
```bash
# Start all services
docker-compose up -d

# Check logs
docker-compose logs -f vllm

# Scale vLLM to multiple GPUs (if available)
docker-compose up -d --scale vllm=2  # Requires --tensor-parallel-size in command
```

## **7. Usage Examples**
### **Direct vLLM API**
```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8000/v1", api_key="any-string")

response = client.chat.completions.create(
    model="Qwen1.5-MoE-A2.7B",
    messages=[{"role": "user", "content": "Explain quantum computing"}]
)
```

### **Through LiteLLM Proxy**
```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:4000", api_key="any-string")

# Will automatically route to Qwen MoE
response = client.chat.completions.create(
    model="qwen-moe",
    messages=[{"role": "user", "content": "Explain quantum computing"}]
)
```

## **8. Monitoring Endpoints**
| Service | URL |
|---------|-----|
| vLLM | `http://localhost:8000` |
| LiteLLM | `http://localhost:4000` |
| Langfuse UI | `http://localhost:3000` |
| Prometheus | `http://localhost:9090` |

## **9. Key Features**
1. **GPU Optimization**: Automatic NVIDIA GPU utilization
2. **Model Routing**: LiteLLM handles fallback to other models
3. **Observability**: All calls logged in Langfuse
4. **Scalability**: Easy to add more GPU workers
5. **Caching**: Models cached locally for faster restarts

## **10. Troubleshooting**
```bash
# Check GPU access
docker run --rm --gpus all nvidia/cuda:11.8.0-base nvidia-smi

# Rebuild containers after config changes
docker-compose up -d --build

# Purge and restart
docker-compose down -v && docker-compose up -d
```

This setup provides a production-ready deployment with:
- ✅ vLLM for optimized inference
- ✅ LiteLLM for model routing
- ✅ Langfuse for observability
- ✅ Docker for easy deployment