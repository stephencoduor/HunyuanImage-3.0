# Runtime image aligned with PyTorch 2.7.1 cu128 wheels
FROM nvidia/cuda:12.8.0-cudnn-runtime-ubuntu24.04

# Set non-interactive frontend
ENV DEBIAN_FRONTEND=noninteractive

# System dependencies and Python 3.12 (per README)
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        python3.12 \
        python3.12-dev \
        python3.12-venv \
        python3-pip && \
    rm -rf /var/lib/apt/lists/* && \
    ln -sf /usr/bin/python3.12 /usr/bin/python3

# Ensure pip is up to date
RUN python3 -m pip install --no-cache-dir --upgrade pip

WORKDIR /workspace/HunyuanImage-3.0

# Copy requirement files first for better build cache
COPY requirements.txt .

ARG TORCH_INDEX_URL=https://download.pytorch.org/whl/cu128

# Install PyTorch with CUDA 12.8 compatibility and Python dependencies
RUN python3 -m pip install --no-cache-dir \
    torch==2.7.1 torchvision==0.22.1 torchaudio==2.7.1 \
    --index-url ${TORCH_INDEX_URL}
RUN python3 -m pip install --no-cache-dir -r requirements.txt

# Optional performance dependencies
ARG INSTALL_FLASH_ATTN=false
ARG INSTALL_FLASH_INFER=false
RUN if [ "$INSTALL_FLASH_ATTN" = "true" ]; then \
        python3 -m pip install --no-cache-dir flash-attn==2.8.3 --no-build-isolation; \
    fi; \
    if [ "$INSTALL_FLASH_INFER" = "true" ]; then \
        python3 -m pip install --no-cache-dir flashinfer-python; \
    fi

# Copy the rest of the repository
COPY . .

# Environment defaults mirroring run_app.sh
ENV GPUS="0,1,2,3" \
    HOST="0.0.0.0" \
    PORT=443 \
    MODEL_ID="HunyuanImage-3/" \
    CUDA_VISIBLE_DEVICES="${GPUS}"

CMD ["bash", "./run_app.sh"]