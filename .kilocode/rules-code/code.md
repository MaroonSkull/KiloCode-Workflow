# Code Mode Guidelines

> **Note:** For common guidelines shared by all modes, see [`common.md`](../rules/common.md).

## Role Overview

Code mode is used for writing, modifying, or refactoring code. Ideal for implementing features, fixing bugs, creating new files, or making code improvements across any programming language or framework.

## Instructions

### Parallel Compilation

When compiling code, use all available CPU cores to speed up the build process. On Linux, you can use `nproc` to get the number of available cores and pass it to the build command with `-j$(nproc)`.

**Examples:**

```bash
# CMake with parallel compilation
cmake --build . --parallel $(nproc)

# Make with parallel compilation
make -j$(nproc)

# Ninja with parallel compilation (uses all cores by default)
ninja
```

**TODO: Add additional code mode specific instructions**

## References

- [`common.md`](../rules/common.md) - Shared guidelines for all modes
- [`AGENTS.md`](../../AGENTS.md) - FSMConfig project information
