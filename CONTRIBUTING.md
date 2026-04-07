# Contributing to amd-rocm-power8-patches

Thank you for your interest in contributing to the amd-rocm-power8-patches project! This repository contains patches for building the ROCm stack on IBM POWER8 (ppc64le) architecture.

## Getting Started

### Prerequisites

- IBM POWER8 system or ppc64le environment
- ROCm 5.7.1 or compatible version
- Git
- Basic knowledge of patch application and CMake build systems

### Setting Up

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Scottcjn/amd-rocm-power8-patches.git
   cd amd-rocm-power8-patches
   ```

2. **Review existing patches:**
   ```bash
   ls -la *.patch
   cat rocm-power8.patch
   ```

3. **Test patch application:**
   ```bash
   cd /path/to/rocm-source
   patch --dry-run -p1 < /path/to/amd-rocm-power8-patches/rocm-power8.patch
   ```

## How to Contribute

### Reporting Issues

If you find a bug or have a suggestion:

1. Check existing issues first
2. Create a new issue with:
   - Clear title and description
   - Steps to reproduce (for bugs)
   - Your POWER8 system details (OS, ROCm version)
   - Expected vs actual behavior

### Submitting Patches

We welcome patches for:

- New ROCm versions
- Additional POWER8 optimizations
- Bug fixes
- Documentation improvements

**Patch Guidelines:**

1. **Format:** Use unified diff format (`diff -u`)
2. **Naming:** Include ROCm version and target component
   - Example: `rocm-5.7.1-hip-power8.patch`
3. **Testing:** Test patches on real POWER8 hardware when possible
4. **Documentation:** Update relevant docs if needed

**Example Patch Submission:**

```bash
# Create your patch
diff -u original_file.c modified_file.c > my-power8-fix.patch

# Test it
patch --dry-run -p1 < my-power8-fix.patch

# Submit via PR
git checkout -b feature/my-power8-fix
git add my-power8-fix.patch
git commit -m "Add POWER8 optimization for XYZ"
git push origin feature/my-power8-fix
```

### Pull Request Process

1. **Fork the repository**
2. **Create a feature branch:** `git checkout -b feature/your-feature`
3. **Make your changes**
4. **Commit with clear messages:**
   - Use present tense: "Add feature" not "Added feature"
   - Be specific: "Fix POWER8 build error in hipBLAS" not "Fix build"
5. **Push to your fork:** `git push origin feature/your-feature`
6. **Create Pull Request**

**PR Checklist:**

- [ ] Code follows project style
- [ ] Patches tested on POWER8 (or documented why not)
- [ ] Documentation updated (if needed)
- [ ] Commit messages are clear
- [ ] No trailing whitespace
- [ ] PR description explains the change

### Code Style

- Use 4 spaces for indentation (no tabs)
- Follow C/C++ coding standards
- Add comments for complex logic
- Keep patches focused and minimal

## Testing

### Testing on POWER8

If you have access to POWER8 hardware:

1. **Apply patches:**
   ```bash
   cd /path/to/rocm-source
   patch -p1 < /path/to/amd-rocm-power8-patches/your-patch.patch
   ```

2. **Build ROCm:**
   ```bash
   mkdir build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j$(nproc)
   ```

3. **Verify:**
   ```bash
   # Check for build errors
   # Test basic ROCm functionality
   ```

### Testing Without POWER8

If you don't have POWER8 access:

1. **Review patch syntax:**
   ```bash
   patch --dry-run -p1 < your-patch.patch
   ```

2. **Check for common issues:**
   - Correct file paths
   - No whitespace errors
   - Proper diff format

## Documentation

### Updating Docs

Documentation files:
- `README.md` - Main project documentation
- `KERNEL_BUILD_STATUS.md` - Kernel compatibility status
- `BCOS.md` - BCOS certification details

When updating docs:
- Keep it simple and clear
- Include examples where helpful
- Update version numbers if relevant

## Getting Help

- **Issues:** Use GitHub Issues for bugs and questions
- **Discussions:** Use GitHub Discussions for ideas and questions
- **Discord:** Join the RustChain Discord for real-time help

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

## Recognition

Contributors will be recognized in:
- CONTRIBUTORS.md (if created)
- Release notes (for significant contributions)
- BCOS certification (for verified patches)

Thank you for contributing to POWER8 ROCm support! 🚀
