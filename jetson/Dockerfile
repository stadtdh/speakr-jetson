# Jetson AGX Orin
# JetPack 6.2 / CUDA 12.x
FROM nvcr.io/nvidia/l4t-pytorch:r36.3.0-pth2.2-py3

ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /app

# ------------------------------------------------------------------
# System dependencies
# ------------------------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    ffmpeg \
    git \
    gcc \
    build-essential \
    wget \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# ------------------------------------------------------------------
# Python tooling
# ------------------------------------------------------------------
RUN python3 -m pip install --upgrade pip

# ------------------------------------------------------------------
# Python dependencies (Jetson-safe)
# ------------------------------------------------------------------
COPY jetson/requirements.txt /tmp/requirements.txt
RUN pip install --no-cache-dir -r /tmp/requirements.txt

# ------------------------------------------------------------------
# Vendor assets (same behavior as upstream)
# ------------------------------------------------------------------
RUN mkdir -p /app/static/vendor
COPY scripts/download_offline_deps.py scripts/
RUN pip install --no-cache-dir requests && \
    PRODUCTION=1 python3 scripts/download_offline_deps.py

# ------------------------------------------------------------------
# Application source
# ------------------------------------------------------------------
COPY . .

# ------------------------------------------------------------------
# Runtime directories
# ------------------------------------------------------------------
RUN mkdir -p /data/uploads /data/instance && \
    chmod 755 /data/uploads /data/instance

# ------------------------------------------------------------------
# Environment
# ------------------------------------------------------------------
ENV FLASK_APP=src/app.py
ENV SQLALCHEMY_DATABASE_URI=sqlite:////data/instance/transcriptions.db
ENV UPLOAD_FOLDER=/data/uploads
ENV PYTHONPATH=/app
ENV HF_HOME=/data/instance/huggingface

EXPOSE 8899

CMD ["gunicorn", "--workers", "2", "--bind", "0.0.0.0:8899", "--timeout", "600", "src.app:app"]
