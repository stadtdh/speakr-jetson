# Jetson AGX Orin (JetPack 6.2) Support

This setup provides GPU-accelerated execution on NVIDIA Jetson AGX Orin.

## Key points
- Uses NVIDIA L4T PyTorch base image
- Compatible with JetPack 6.x (Ubuntu 22.04, CUDA 12)
- PyTorch is NOT installed via pip
- CUDA support is enabled automatically via NVIDIA Container Runtime

## Build
docker build -f docker/jetson/Dockerfile -t speakr-jetson .

## Run
docker run --rm -it \
  --runtime nvidia \
  --network host \
  speakr-jetson