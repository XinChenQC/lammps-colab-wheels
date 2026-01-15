# lammps-colab wheel (serial)

Prebuilt serial (CPU-only, no MPI) LAMMPS Python wheels.

```bash
%%bash
set -e
cd /content/lammps

rm -rf build-serial-full
mkdir build-serial-full
cd build-serial-full

cmake -G Ninja ../cmake \
  -D CMAKE_BUILD_TYPE=Release \
  -D BUILD_MPI=OFF \
  -D BUILD_SHARED_LIBS=ON \
  -D PKG_PYTHON=ON \
  -D PKG_MANYBODY=ON \
  -D PKG_MOLECULE=ON \
  -D PKG_KSPACE=ON \
  -D PKG_RIGID=ON \
  -D PKG_REAXFF=ON \
  -D PKG_MC=ON \
  -D PKG_GRANULAR=ON \
  -D PKG_SHOCK=ON \
  -D PKG_USER-MISC=ON \
  -D PKG_USER-REAXC=ON \
  -D PKG_EXTRA-FIX=ON \
  -D PKG_EXTRA-PAIR=ON \
  -D PKG_EXTRA-COMPUTE=ON \
  -D PKG_EXTRA-DUMP=ON

ninja -j"$(nproc)"
```


## Install

```bash
pip install https://github.com/XinChenQC/lammps-colab-wheels/releases/download/0.0/lammps_colab-0.0.0-cp312-cp312-linux_x86_64.whl
```

# lammps-colab-T4 wheel (serial)

Prebuilt GPU (CPU-serial, no MPI) LAMMPS Python wheels.

```bash
set -e
cd /content/lammps

GPU_NAME="$(nvidia-smi --query-gpu=name --format=csv,noheader | head -n 1 || true)"
echo "GPU_NAME=$GPU_NAME"

KOKKOS_ARCH=""
case "$GPU_NAME" in
  *"T4"*)   KOKKOS_ARCH="TURING75" ;;
  *"P100"*) KOKKOS_ARCH="PASCAL60" ;;
  *"V100"*) KOKKOS_ARCH="VOLTA70" ;;
  *"A100"*) KOKKOS_ARCH="AMPERE80" ;;
  *"A10"*)  KOKKOS_ARCH="AMPERE86" ;;
  *"L4"*)   KOKKOS_ARCH="AMPERE89" ;;
  *"L40"*)  KOKKOS_ARCH="ADA89" ;;          
  *"RTX 30"*) KOKKOS_ARCH="AMPERE86" ;;
  *) echo "WARN: Unknown GPU; defaulting to TURING75 (works for T4)."
     KOKKOS_ARCH="TURING75" ;;
esac
echo "KOKKOS_ARCH=$KOKKOS_ARCH"

rm -rf build-kokkos-cuda
mkdir build-kokkos-cuda
cd build-kokkos-cuda

cmake -G Ninja ../cmake \
  -D CMAKE_BUILD_TYPE=Release \
  -D BUILD_MPI=OFF \
  -D BUILD_SHARED_LIBS=ON \
  -D PKG_PYTHON=ON \
  -D PKG_KOKKOS=ON \
  -D Kokkos_ENABLE_CUDA=ON \
  -D Kokkos_ENABLE_CUDA_LAMBDA=ON \
  -D Kokkos_ENABLE_SERIAL=ON \
  -D Kokkos_ARCH_${KOKKOS_ARCH}=ON \
  -D CMAKE_CUDA_COMPILER="$(command -v nvcc)" \
  -D CMAKE_CUDA_STANDARD=17 \
  -D CMAKE_CUDA_STANDARD_REQUIRED=ON \
  -D CMAKE_CUDA_EXTENSIONS=OFF \
  -D CMAKE_CUDA_FLAGS="-lineinfo" \
  -D PKG_MANYBODY=ON \
  -D PKG_MOLECULE=ON \
  -D PKG_KSPACE=ON \
  -D PKG_RIGID=ON \
  -D PKG_REAXFF=ON \
  -D PKG_MC=ON \
  -D PKG_GRANULAR=ON \
  -D PKG_SHOCK=ON \
  -D PKG_USER-MISC=ON \
  -D PKG_USER-REAXC=ON \
  -D PKG_EXTRA-FIX=ON \
  -D PKG_EXTRA-PAIR=ON \
  -D PKG_EXTRA-COMPUTE=ON \
  -D PKG_EXTRA-DUMP=ON

ninja -j"$(nproc)"
```
