Reflect on 5-7 different possible sources of the problem, distill those down to 1-2 most likely sources, and then add logs to validate your assumptions. Explicitly ask the user to confirm the diagnosis before fixing the problem.

---

# Debug Mode Guidelines

> **Note:** For common guidelines shared by all modes, see [`common.md`](../rules/common.md).

## Role Overview

Debug mode is used when troubleshooting issues, investigating errors, or diagnosing problems. Specialized in systematic debugging, adding logging, analyzing stack traces, and identifying root causes before applying fixes.

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

**TODO: Add additional debug mode specific instructions**

## References

- [`common.md`](../rules/common.md) - Shared guidelines for all modes
- [`AGENTS.md`](../../AGENTS.md) - FSMConfig project information
