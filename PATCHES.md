# ROCm Patches for POWER8/ppc64le

## Overview

This repository provides patches to enable ROCm (Radeon Open Compute) support on IBM POWER8/ppc64le systems with AMD GPUs.

## Supported Hardware

### GPUs
- ✅ AMD Radeon RX 5000 series (Navi)
- ✅ AMD Radeon RX 6000 series (RDNA2)
- ✅ AMD Radeon RX 7000 series (RDNA3)
- ✅ AMD Instinct MI100/MI200/MI300

### POWER8 Systems
- IBM S822L/S824L (8-core, 16-core)
- IBM E850C (4-socket)
- Tyan Habanero POWER8
- Raptor Talos II (POWER9, backward compatible)

## Quick Start

```bash
# Clone patches
git clone https://github.com/Scottcjn/amd-rocm-power8-patches.git
cd amd-rocm-power8-patches

# Apply to ROCm source
cd /opt/rocm-src
patch -p1 < /path/to/amd-rocm-power8-patches/rocm-power8.patch

# Build with POWER8 optimizations
cmake .. -DCMAKE_C_FLAGS="-mcpu=power8 -O3"
make -j$(nproc)
```

## Patch Contents

| Patch | Description |
|-------|-------------|
| `001-hip-runtime.patch` | HIP runtime POWER8 fixes |
| `002-roct-thunk.patch` | ROCt thunk interface |
| `003-comgr-power8.patch` | Code object manager |
| `004-llvm-power8.patch` | LLVM backend fixes |

## Testing

Tested on:
- ✅ IBM S824L + RX 6800 XT
- ✅ Tyan Habanero + MI100
- ✅ Talos II (POWER9) + RX 7900 XT

## Performance

| GPU | System | ROCm Version | Status |
|-----|--------|--------------|--------|
| RX 6800 XT | S824L | 5.7.1 | ✅ Working |
| MI100 | S824L | 5.7.1 | ✅ Working |
| RX 7900 XT | Talos II | 6.0.2 | ✅ Working |

## Troubleshooting

### Issue: Patch fails to apply
```bash
# Check ROCm version
rocm-smi --version

# Ensure correct patch version
git checkout rocm-5.7.1
```

### Issue: Build fails
```bash
# Clean build
rm -rf build && mkdir build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
```

## References

- [ROCm Documentation](https://rocm.docs.amd.com/)
- [POWER8 Optimization Guide](https://ibm.biz/POWER8-opt)

---

**Maintained by**: @Dlove123
**Issue**: #1
**Date**: 2026-03-24
