<div align="center">

# ROCm for AMD GPUs on IBM POWER8

[![POWER8](https://img.shields.io/badge/POWER8-ppc64le-blue)](https://github.com/Scottcjn/amd-rocm-power8-patches)
[![ROCm](https://img.shields.io/badge/ROCm-5.7.1-red)](https://rocm.docs.amd.com/)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)
[![AMD](https://img.shields.io/badge/AMD-RX%20580-green)](https://github.com/Scottcjn/amd-rocm-power8-patches)

**World's First ROCm Stack Built from Source for POWER8/PPC64LE**

*Run AMD GPU compute on IBM POWER8 servers - an alternative to NVIDIA CUDA*

[Build Status](#-rocm-build-status) • [Quick Start](#-build-instructions) • [Hardware](#-test-system) • [Why ROCm?](#-why-rocm-on-power8)

</div>

---

## 🎯 What This Enables

| Traditional Setup | With ROCm on POWER8 |
|------------------|---------------------|
| NVIDIA-only compute | AMD GPU alternative |
| CUDA lock-in | HIP portability |
| Expensive datacenter GPUs | Affordable Polaris/Vega |
| GPU VRAM limited | 576GB system RAM + GPU |

## ✅ ROCm Build Status

| Component | Version | Status | Notes |
|-----------|---------|--------|-------|
| **ROCT-Thunk-Interface** | 5.7.1 | ✅ Built | HSA kernel interface |
| **LLVM/Clang** | 17.0.0 | ✅ Built | AMDGPU + PowerPC targets |
| **ROCR-Runtime** | 5.7.1 | ✅ Built | libhsa-runtime64.so |
| **Linux Kernel** | 6.1.159 | ✅ Built | PowerElyan HSA kernel |

### Current Hardware Status: RX 580 Detected!

```
Device: 1002:67df (AMD Ellesmere - RX 580)
Driver: amdgpu loaded ✅
VRAM: 8192MB detected ✅
DRM: /dev/dri/card0, /dev/dri/renderD128 ✅
```

## 🖥️ Test System

| Component | Specification |
|-----------|---------------|
| **Server** | IBM Power System S824 (8286-42A) |
| **CPU** | Dual 8-core POWER8 (128 threads w/ SMT8) |
| **RAM** | 576 GB DDR3 |
| **GPU** | AMD RX 580 8GB (via OCuLink) |
| **OS** | Ubuntu 20.04 LTS |

## 🔧 Build Instructions

### Prerequisites (Built from Source)

Ubuntu 20.04 on POWER8 has broken packages. All prerequisites built from source:

```bash
# cmake 3.25.3
wget https://github.com/Kitware/CMake/releases/download/v3.25.3/cmake-3.25.3.tar.gz
tar xzf cmake-3.25.3.tar.gz && cd cmake-3.25.3
./bootstrap --prefix=/usr/local -- -DCMAKE_USE_OPENSSL=OFF
make -j$(nproc) && sudo make install

# numactl 2.0.16
wget https://github.com/numactl/numactl/releases/download/v2.0.16/numactl-2.0.16.tar.gz
tar xzf numactl-2.0.16.tar.gz && cd numactl-2.0.16
./configure --prefix=/usr/local && make -j$(nproc) && sudo make install

# libdrm 2.4.100 (AMD only)
wget https://dri.freedesktop.org/libdrm/libdrm-2.4.100.tar.bz2
tar xjf libdrm-2.4.100.tar.bz2 && cd libdrm-2.4.100
./configure --prefix=/usr/local --disable-nouveau --enable-amdgpu
make -j$(nproc) && sudo make install
```

### Build ROCT-Thunk-Interface

```bash
wget https://github.com/RadeonOpenCompute/ROCT-Thunk-Interface/archive/refs/tags/rocm-5.7.1.tar.gz
tar xzf rocm-5.7.1.tar.gz && cd ROCT-Thunk-Interface-rocm-5.7.1
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/rocm
make -j$(nproc) && sudo make install
```

### Build LLVM/Clang with AMDGPU

```bash
wget https://github.com/RadeonOpenCompute/llvm-project/archive/refs/tags/rocm-5.7.1.tar.gz
tar xzf rocm-5.7.1.tar.gz && cd llvm-project-rocm-5.7.1
mkdir build && cd build

cmake ../llvm \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/opt/rocm/llvm \
    -DLLVM_ENABLE_PROJECTS="clang;lld" \
    -DLLVM_TARGETS_TO_BUILD="AMDGPU;PowerPC" \
    -DLLVM_ENABLE_ASSERTIONS=OFF

make -j64 && sudo make install  # 64 threads optimal on POWER8
```

### Build ROCR-Runtime

```bash
wget https://github.com/RadeonOpenCompute/ROCR-Runtime/archive/refs/tags/rocm-5.7.1.tar.gz
tar xzf rocm-5.7.1.tar.gz && cd ROCR-Runtime-rocm-5.7.1
mkdir build && cd build

cmake ../src \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/opt/rocm \
    -DCMAKE_C_COMPILER=/opt/rocm/llvm/bin/clang \
    -DCMAKE_CXX_COMPILER=/opt/rocm/llvm/bin/clang++

make -j32 && sudo make install
```

## ✅ Verification

```bash
# Check Clang
$ /opt/rocm/llvm/bin/clang --version
clang version 17.0.0
Target: powerpc64le-unknown-linux-gnu

# Check libraries
ls -la /opt/rocm/lib/libhsa*
```

## 🎮 Supported AMD GPUs

| Architecture | GPUs | Status |
|-------------|------|--------|
| **RDNA 2/3** | RX 6800, 7900 XTX | Full ROCm |
| **RDNA 1** | RX 5700 | Full ROCm |
| **Polaris** | RX 580, 480 | Legacy (5.7.x) |
| **Vega** | Radeon VII, MI50 | Full ROCm |

## 🔌 OCuLink Connection

Same method as NVIDIA GPUs:

```
┌─────────────────────┐         ┌──────────────────────┐
│   IBM POWER8 S824   │         │   External GPU       │
│  ┌───────────────┐  │ OCuLink │  ┌────────────────┐  │
│  │ Supermicro    │◄─┼─────────┼──│ Minisforum DEG1│  │
│  │ AOC-SLG3-2E4T │  │ Cable   │  │ + RX 580       │  │
│  └───────────────┘  │         │  └────────────────┘  │
└─────────────────────┘         └──────────────────────┘
```

## 🤔 Why ROCm on POWER8?

1. **True Open Source** - AMD's compute stack is fully open
2. **No Vendor Lock-in** - HIP code runs on AMD or NVIDIA
3. **Affordable GPUs** - Polaris/Vega cards are cheap
4. **576GB RAM** - Massive system memory complements GPU
5. **Alternative Ecosystem** - Not dependent on NVIDIA

## 🔗 Related Projects

| Project | Description |
|---------|-------------|
| [nvidia-power8-patches](https://github.com/Scottcjn/nvidia-power8-patches) | NVIDIA drivers for POWER8 |
| [cuda-power8-patches](https://github.com/Scottcjn/cuda-power8-patches) | CUDA toolkit for POWER8 |
| [power8-projects](https://github.com/Scottcjn/power8-projects) | PowerElyan Linux builds |

## 📋 Next Steps

- [x] Build ROCm userspace stack
- [x] Test RX 580 via OCuLink
- [ ] Boot PowerElyan kernel with CONFIG_HSA_AMD=y
- [ ] Verify `/dev/kfd` and rocminfo
- [ ] Build HIP runtime
- [ ] Benchmark RX 580 compute

## 📜 License

MIT License - Build instructions and patches.
ROCm components retain AMD's original licenses.

---

<div align="center">

**Made with ⚡ by [Elyan Labs](https://elyanlabs.ai)**

*AMD GPU compute on POWER8 - because vendor choice matters*

</div>


## 中文简介

Elyan Labs POWER 项目 - 为 IBM POWER 和复古系统提供现代支持。

Contributed by eelaine-wzw
