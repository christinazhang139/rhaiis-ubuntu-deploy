# Red Hat AI Inference Server Deployment Guide on Ubuntu

Launch a local AI inference server with GPU-accelerated model services  
(Takes approximately 15 minutes)

## System Requirements

- **Operating System**: Ubuntu
- **GPU**: RTX 4090 (24GB VRAM)
- **Container Runtime**: Docker or Podman

## Detailed Guide

### 1. Verify GPU Status

```bash
# Check if GPU is working properly
nvidia-smi
```

> **Note**: To check the appropriate NVIDIA driver and CUDA versions for your machine, please refer to [nvidia_cuda_compatibility.md](nvidia_cuda_compatibility.md)

### 2. Verify Docker GPU Support

```bash
# Test Docker GPU access
docker run --rm --gpus all nvidia/cuda:11.8-base-ubuntu20.04 nvidia-smi
```

If the above command displays GPU information normally, the environment is ready.

## Step 1: Login and Pull Image

### Login to Red Hat Container Registry

```bash
# Login with Red Hat account
docker login registry.redhat.io
# Enter username and password
```

### Pull Red Hat AI Inference Server Image

```bash
# Pull latest version image (3.2.0 build 1752784628) - this will take some time
# https://catalog.redhat.com/en find latest version here, search with keyword 'rhaiis'
docker pull registry.redhat.io/rhaiis/vllm-cuda-rhel9:3.2.0-1752784628
```

## Step 2: Prepare Working Directory and Authentication

```bash
# Create cache directory (for storing models)
mkdir -p ~/rhaiis-cache

# Create configuration directory
mkdir -p ~/rhaiis-config

# Set appropriate permissions (777 ensures container user can write)
chmod 777 ~/rhaiis-cache ~/rhaiis-config

# Set Hugging Face Token (required for accessing Red Hat models)
export HF_TOKEN="xxxxxxx"
```

## Step 3: Ultra-lightweight Setup

**Recommended for**: Quick environment verification, first-time testing

```bash
export HF_TOKEN="xxxxxx"

docker run -d \
    --gpus all \
    -p 8000:8000 \
    -v ~/rhaiis-cache:/opt/app-root/src/.cache \
    --shm-size=4g \
    --name rhaiis-server \
    --restart unless-stopped \
    -e HUGGING_FACE_HUB_TOKEN=$HF_TOKEN \
    -e CUDA_VISIBLE_DEVICES=0 \
    registry.redhat.io/rhaiis/vllm-cuda-rhel9:3.2.0-1752784628 \
    --model RedHatAI/Llama-3.2-1B-Instruct-quantized.w8a8 \
    --host 0.0.0.0 \
    --port 8000 \
    --max-model-len 4096 \
    --max-num-seqs 32 \
    --tensor-parallel-size 1 \
    --enforce-eager \
    --disable-custom-all-reduce
```

## Verify Deployment

### Check Service Status

```bash
# View container running status
docker ps

# View service logs
docker logs -f rhaiis-server

# Check service health status
curl http://localhost:8000/health
```

### Test API Functionality

```bash
# View available models
curl http://localhost:8000/v1/models

# Test text generation
curl http://localhost:8000/v1/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "RedHatAI/Llama-3.2-1B-Instruct-quantized.w8a8",
        "prompt": "Hello, I am",
        "max_tokens": 20
    }'
```

## Signs of Successful Deployment

The following logs indicate successful deployment:
```
INFO [api_server.py] vLLM API server version 0.9.2
INFO [config.py] Using max model len 4096
INFO [api_server.py] Started engine process with PID XX
INFO [api_server.py] Uvicorn running on http://0.0.0.0:8000
```

## Service Management

```bash
# Stop service
docker stop rhaiis-server

# Restart service
docker restart rhaiis-server

# Remove service
docker rm -f rhaiis-server
```

## Troubleshooting

If you encounter issues:

1. **Check GPU drivers**: Ensure `nvidia-smi` works properly
2. **Check Docker GPU support**: Ensure Docker can access GPU
3. **Check container logs**: Use `docker logs rhaiis-server` to view error messages
4. **Check port usage**: Ensure port 8000 is not occupied

---

**Important Notes**:
- First run will download model files, which takes some time
- Ensure sufficient disk space for storing models
- RTX 4090's 24GB VRAM is sufficient for running most quantized models
