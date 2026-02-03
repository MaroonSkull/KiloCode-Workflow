Prioritize test readability, comprehensive edge cases, and clear assertion messages. Always consider both happy path and error scenarios.

---

# Test Engineer Mode Guidelines

> **Note:** For common guidelines shared by all modes, see [`common.md`](../rules/common.md).

## Role Overview

Test Engineer mode is a QA engineer and testing specialist focused on writing comprehensive tests, debugging failures, and improving code coverage.

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

**TODO: Add additional test-engineer mode specific instructions**

## References

- [`common.md`](../rules/common.md) - Shared guidelines for all modes
- [`AGENTS.md`](../../AGENTS.md) - FSMConfig project information
