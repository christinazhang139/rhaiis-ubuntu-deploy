# NVIDIA Driver & CUDA Version Compatibility Table

## For Red Hat AI Inference Server Deployment

### RTX 4090 Recommended Configuration

| NVIDIA Driver Version | Supported CUDA Version | Red Hat AI Inference Server | Flash Attention Support | Recommendation Level | Notes |
|----------------------|------------------------|----------------------------|----------------------|-------------------|-------|
| **550.54.14+** | **CUDA 12.4** | **✅ 3.2.0** | **✅ Full Support** | **🏆 Highly Recommended** | Official minimum requirement, optimal performance |
| 545.23.06+ | CUDA 12.3 | ✅ 3.2.0 | ✅ Full Support | 🟢 Recommended | Stable version |
| 535.54.03+ | CUDA 12.2 | ✅ 3.1.0 | ✅ Supported* | 🟡 Usable | *Requires manual compilation or xformers fallback |
| 525.60.13+ | CUDA 12.0 | ✅ 3.0.0 | ❌ Not Supported | 🟡 Basic Usable | Flash-Attn2 requires CUDA≥12.1 |
| 520.61.05+ | CUDA 11.8 | ⚠️ Limited Support | ❌ Not Supported | 🔴 Not Recommended | RTX 4090 requires ≥520.56.06 |

### Current Mainstream GPU Compatibility

| GPU Model | Minimum Driver | Recommended CUDA | Flash Attention | Notes |
|-----------|---------------|-----------------|-----------------|-------|
| **RTX 4090** | **520.56.06+** | **CUDA 12.3+** | **✅ Supported** | Ada architecture launch driver, recommend ≥545 with newer CUDA |
| RTX 4080 | 520.56.06+ | CUDA 12.3+ | ✅ Supported | Same Ada architecture |
| RTX 4070 Ti | 520.56.06+ | CUDA 12.3+ | ✅ Supported | Same Ada architecture |
| RTX 3090 | 470.129.06+ | CUDA 11.8+ | ✅ Supported | Ampere architecture |
| RTX 3080 | 470.129.06+ | CUDA 11.8+ | ✅ Supported | Ampere architecture |

### Red Hat AI Inference Server Version Compatibility

| Container Version | Built-in CUDA Version | Minimum Driver | Recommended Driver | Status |
|------------------|----------------------|---------------|-------------------|--------|
| **3.2.0-1752784628** | **CUDA 12.4** | **550.54.14+** | **550.163+** | **Current Latest** |
| 3.1.0 | CUDA 12.3 | 545.23.06+ | 550.54.14+ | Stable version |
| 3.0.0 | CUDA 12.2 | 535.54.03+ | 545.23.06+ | Older version |

### Check Current Configuration

```bash
# Check current NVIDIA driver version
nvidia-smi

# Check CUDA version
nvcc --version

# Check GPU compute capability
nvidia-smi --query-gpu=compute_cap --format=csv,noheader,nounits
```
