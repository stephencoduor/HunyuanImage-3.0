# HunyuanImage 3.0 Helm Chart

This chart deploys the HunyuanImage 3.0 application with GPU acceleration and persistent storage for model weights.

## Prerequisites
- Kubernetes cluster with NVIDIA GPU nodes exposing `nvidia.com/gpu`
- [NVIDIA device plugin](https://github.com/NVIDIA/k8s-device-plugin) installed
- Storage class capable of providing at least 200Gi for model weights (170Gi minimum from the project README)

## Installation
```bash
# From the repository root
git clone <repo-url> && cd HunyuanImage-3.0
helm install hunyuanimage3 ./helm/hunyuanimage3 -n hunyuan --create-namespace \
  --set image.repository=<your-image> \
  --set image.tag=<tag> \
  --set persistence.storageClass=<storage-class-name>
```

### Common Overrides
- **Service type**: switch to NodePort with `--set service.type=NodePort --set service.nodePort=<port>`
- **GPU count**: adjust `resources.requests.nvidia.com/gpu` and `resources.limits.nvidia.com/gpu`
- **Probes**: change probe type or timing under `probes.*`
- **Command/Args/Env**: override `command`, `args`, or `env` entries to pass flags to `run_app.sh`
- **Private registries**: set `imagePullSecrets` to reference pre-created registry credentials

## Persistence
By default, the chart creates a `PersistentVolumeClaim` named `<release>-hunyuanimage3-weights` mounted at `/workspace/HunyuanImage-3.0/model_weights`. Bind it to a fast storage class when possible.

## GPU Scheduling
The chart ships with GPU-friendly tolerations and node affinity targeting nodes labeled `nvidia.com/gpu.present=true`. Update these selectors to match your cluster’s GPU node labels.
