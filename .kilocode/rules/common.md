# Common AI Agent Guidelines

## General Work Guidelines

**ALWAYS prefer MCP tools over console commands when possible.**

### Using Context

For effective work with the project, ALWAYS use:
1. **Context7** — to get up-to-date documentation for any library
2. **MCP Memory Server** — for saving and retrieving knowledge about the project

### Rules for Creating .md Files

**MANDATORY RULE:** All `.md` files must be created exclusively inside the `docs/` directory.

**Exceptions:** [`README.md`](../../README.md), [`AGENTS.md`](../../AGENTS.md), [`LICENSE`](../../LICENSE), [`DOCKER.md`](../../DOCKER.md) at project root.

#### Permanent Documentation (Versioned)

Tracked in git:
- **API Documentation:** [`docs/api_reference.md`](../api_reference.md), [`docs/architecture.md`](../architecture.md)
- **Development Guides:** [`docs/development/`](../development/)
- **Architecture Decision Records:** `docs/architecture/decisions/` — ONLY finalized ADRs

#### Temporary Documentation (Work in Progress)

NOT tracked in git:
- **Location:** `docs/.wip/<branch-name>/`
- **Purpose:** Temporary drafts, ADRs, design documents
- **Cleanup:** After task completion, delete or move to permanent docs

**Example:**
```bash
# 1. Create branch
git checkout -b feature/add-state-persistence

# 2. AI creates temporary files in docs/.wip/feature/add-state-persistence/

# 3. After completion - move important ADRs to docs/architecture/decisions/

# 4. Clean up
rm -rf docs/.wip/feature/add-state-persistence/
```

**IMPORTANT:** NEVER create .md files directly in `docs/` root (except permanent files). ALWAYS use `docs/.wip/<branch-name>/` for task-specific documentation.

### MCP Memory Server

**Purpose:** Provides LLM with knowledge graph capabilities for the project.

**Available tools:**
- `create_entities` — create entities (classes, files, libraries)
- `create_relations` — create relations between entities
- `add_observations` — add observations to existing entities
- `read_graph` — read entire knowledge graph
- `search_nodes` — search entities by name, type, or content
- `open_nodes` — get detailed information about specific entities

**Entity types:** `project`, `class`, `file`, `library`, `tool`, `concept`

**Relation types:** `depends_on`, `uses`, `part_of`, `implements`, `tests`, `extends`

**Workflow:** Check existence → create if needed → add observations → create relations

### High-Level Task Execution Pipeline

**MANDATORY VALIDATION PROTOCOL:** This pipeline enforces the planning-criticism-planning cycle through explicit validation gates. For the full protocol, see [`ADR-003: Mandatory Validation Protocol`](../../docs/architecture/decisions/adr-003-mandatory-validation-protocol.md).

```
1. Make a small action
2. [VALIDATION GATE 1] Switch to code-skeptic mode for critical analysis
   - Analyze results for potential issues
   - Document findings (even if no issues found)
   - PROHIBITED: Do NOT make immediate fixes after error detection
3. If errors/shortcomings found:
   - STOP: Do not proceed with fixes
   - SWITCH to appropriate mode (code-skeptic or code-reviewer)
   - ANALYZE: Critically analyze the root cause
   - REPLAN: Update the plan to address the issue
   - REVALIDATE: Pass through the validation gate again
4. If no errors → continue to next action or validation gate
```

**Key Requirements:**
- **MUST** switch to code-skeptic mode after each action for critical analysis
- **MUST** document all analysis findings
- **PROHIBITED:** Making immediate fixes after error detection without re-planning
- **MUST** receive explicit confirmation before proceeding past validation gates

### High-Level Task Solving Process

```
1. Create architectural ADRs (docs/architecture/decisions/)
2. Add diagrams (component, data flow)
3. Document each critical path
4. Conduct code audit (DRY, logic errors, architectural compliance)
```

**ADR Structure:**
```markdown
# ADR-001: [Title]

## Status
Proposed | Accepted | Deprecated

## Context
[Problem statement]

## Decision
[Solution chosen]

## Consequences
Pros: ... Cons: ...
```

### Using Linters

**Principle:** Use the widest possible set of linters for high code quality.

**Mandatory checks:**
```bash
# clang-tidy
clang-tidy src/**/*.cpp -- -Iinclude/ -std=c++20

# cppcheck
cppcheck --enable=all --inconclusive --std=c++20 src/

# clang-format
clang-format -i src/**/*.cpp include/**/*.hpp
```

### Finding Libraries

**Philosophy:** Don't reinvent the wheel — look for ready-made solutions.

**Process:**
1. Search the internet (Context7) when new functionality is needed
2. Goal: create simple code using existing libraries
3. Don't fear an abundance of dependencies
4. During development, DO NOT implement new methods yourself
5. Propose library integration to user → wait for approval → integrate

---

## Clang-Tidy Configuration Guide

### Overview

FSMConfig uses comprehensive clang-tidy configuration with **~250+ enabled checks** across 8 categories.

**Configuration file:** [`.clang-tidy`](../../.clang-tidy) at project root

**Key options:**
```yaml
InheritParentConfig: false
HeaderFilterRegex: 'include/fsmconfig/.*'
FormatStyle: file
WarningsAsErrors: ''
```

### Configuration Summary

| Category | Checks | Purpose |
|----------|--------|---------|
| `bugprone-*` | ~60 | Catches common programming mistakes |
| `cert-*` | ~30 | Enforces CERT C++ security standards |
| `clang-analyzer-*` | ~50 | Deep static analysis |
| `cppcoreguidelines-*` | ~80 | Enforces C++ Core Guidelines |
| `misc-*` | ~20 | Miscellaneous code quality checks |
| `modernize-*` | ~40 | Modernizes C++ code |
| `performance-*` | ~30 | Performance optimizations |
| `readability-*` | ~50 | Improves readability |

### Usage Guide

```bash
# Basic usage
clang-tidy src/**/*.cpp -- -Iinclude/ -std=c++20

# With compilation database
cd build && cmake .. -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
clang-tidy -p build src/**/*.cpp

# Fix automatically
clang-tidy -p build src/**/*.cpp --fix

# Export to file
clang-tidy src/**/*.cpp -- -Iinclude/ -std=c++20 > report.txt

# Treat warnings as errors (strict CI mode)
clang-tidy src/**/*.cpp -warnings-as-errors='*' -- -Iinclude/ -std=c++20
```

### Integration with CMake

**Option 1: Custom target** (add to [`CMakeLists.txt`](../../CMakeLists.txt)):
```cmake
find_program(CLANG_TIDY clang-tidy)
if(CLANG_TIDY)
    add_custom_target(clang-tidy
        COMMAND ${CLANG_TIDY} -p ${CMAKE_BINARY_DIR} ${PROJECT_SOURCE_DIR}/src/**/*.cpp
        COMMENT "Running clang-tidy..."
        VERBATIM
    )
endif()
```

**Option 2: Automatic during compilation:**
```cmake
set(CMAKE_CXX_CLANG_TIDY clang-tidy;-warnings-as-errors=*)
```

### CI/CD Integration

**GitHub Actions:**
```yaml
- name: Run clang-tidy
  run: |
    clang-tidy -p build src/**/*.cpp > clang-tidy-output.txt || true
```

**Pre-commit hook:**
```bash
#!/bin/sh
CHANGED_CPP=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(cpp|hpp)$')
if [ -n "$CHANGED_CPP" ]; then
    clang-tidy $CHANGED_CPP -- -Iinclude/ -std=c++20
fi
```

### IDE Integration

**VS Code** ([`.vscode/settings.json`](../../.vscode/settings.json)):
```json
{
    "C_Cpp.default.clangTidyEnable": true
}
```

**CLion:** Settings → Languages & Frameworks → C/C++ → Clang-Tidy → Enable

### Check Categories Explained

**bugprone-\***: Catches common bugs (string handling, pointer arithmetic, memory leaks)

**cert-\***: CERT C++ security standards (exception handling, resource management)

**clang-analyzer-\***: Deep static analysis (null dereferences, memory leaks, symbolic execution)

**cppcoreguidelines-\***: C++ Core Guidelines (RAII, smart pointers, modern C++ practices)

**misc-\***: Miscellaneous (const correctness, include cycles, redundant expressions)

**modernize-\***: Modern C++ features (auto, nullptr, range-based for, move semantics)

**performance-\***: Performance optimizations (unnecessary copies, move semantics)

**readability-\***: Code readability (naming conventions, function length, redundant code)

### Excluded Checks

| Check | Reason |
|-------|--------|
| `bugprone-easily-swappable-parameters` | False positive: FSMConfig uses semantically distinct similar types |
| `readability-function-cognitive-complexity` | False positive: Template-heavy FSM logic naturally complex |
| `cppcoreguidelines-avoid-magic-numbers` | Style choice: State IDs used for clarity |
| `misc-const-correctness` | Style choice: Public API requires careful balance |
| `modernize-use-trailing-return-type` | Style choice: Traditional syntax preferred |

---

## Coding Standards

### General Rules

- **Documentation language:** English (technical terms, commands, libraries remain in English)
- **Code mentions:** Wrap in backticks (\`)
- **Doxygen format:** Java style (`/** @... */`)

### Single-Line Comments

```cpp
/// Registers a new callback for the specified event
void registerCallback(const std::string& event, CallbackFunc callback);
```

### Multi-Line Comments

```cpp
/**
 * @brief Parses YAML configuration and creates a finite state machine
 * @param config_path Path to the YAML configuration file
 * @return std::unique_ptr<StateMachine> Pointer to the created state machine
 * @throws std::runtime_error If the configuration is invalid
 */
std::unique_ptr<StateMachine> parseConfig(const std::string& config_path);
```

**IMPORTANT:** The `///` comment must ALWAYS be BEFORE the element being commented.

### Comment Content

**Describe business logic, not algorithms:**

❌ **Bad:** `/// Iterate through all array elements and check the condition`

✅ **Good:** `/// Processes all events from the queue and executes state transitions`

### Documentation Requirements

Document ALL functions touched during work:
- Public class methods
- Protected methods (if part of API)
- Private methods (if non-trivial logic)
- Free functions, structures, classes, enumerations

---

## Critical Development Approach

### Philosophy

**Question every decision:**
- Every decision must be justified
- Relate decisions to existing parts of the system
- Find errors in plans and iteratively eliminate them

### Development Cycle (MANDATORY)

**MANDATORY ENFORCEMENT:** The following cycle is REQUIRED for all non-trivial tasks. Skipping any stage is PROHIBITED. For the full validation protocol, see [`ADR-003: Mandatory Validation Protocol`](../../docs/architecture/decisions/adr-003-mandatory-validation-protocol.md).

```
Plan → Criticize → (Issues found? → Rework → Criticize) → Implement → Review → (Issues? → Rework → Review) → Complete
```

**Key principles:**
1. **Every plan MUST be criticized** before implementation
2. **Every implementation MUST be reviewed** before completion
3. **Every issue triggers rework and re-criticism** before proceeding
4. **No shortcuts** through the cycle are permitted

### Mandatory Validation Gates

**Gate 1: Pre-Implementation Validation**
- **MUST** switch to `code-skeptic` mode for critical analysis
- **MUST** analyze the plan for potential issues
- **MUST** document findings (even if no issues found)
- **CANNOT proceed** until analysis is complete and documented

**Gate 2: Post-Implementation Validation**
- **MUST** switch to `code-reviewer` mode for thorough review
- **MUST** verify all changes against the plan
- **MUST** check for unintended side effects
- **CANNOT proceed** until review is complete and documented

**Gate 3: Subtask Completion Validation**
- **MUST** switch to `code-skeptic` mode for subtask review
- **MUST** verify subtask meets acceptance criteria
- **MUST** check integration with existing code
- **CANNOT proceed** until validation is complete and documented

### Prohibition of Immediate Fixes

**MANDATORY RULE:** When errors or issues are detected at any validation gate:

1. **STOP** - Do NOT make immediate fixes
2. **SWITCH** - Switch to appropriate mode (code-skeptic or code-reviewer)
3. **ANALYZE** - Critically analyze the root cause
4. **REPLAN** - Update the plan to address the issue
5. **REVALIDATE** - Pass through the appropriate gate again

**Rationale:** Immediate fixes bypass critical thinking and may introduce new issues or fail to address root causes.

### During Integration

**Re-verify the codebase:**

1. **DRY (Don't Repeat Yourself):** Search for duplicate code, extract common parts
2. **Search for Existing Elements:** Check if functionality already exists
3. **Search for Ready-Made Libraries:** Use Context7, find alternatives, propose to user

### Example of Critical Analysis

1. Why exactly this solution?
2. Is there a simpler way?
3. Does this violate existing patterns?
4. Will this create problems in the future?
5. Do ready-made libraries exist?
6. Am I duplicating existing code?
7. Will tests cover this change?
8. Is this change sufficiently documented?

### Tools for Critical Analysis (REQUIRED MODES)

**MANDATORY MODE SWITCHING:** The following modes are REQUIRED for critical analysis at validation gates. Switching to these modes is NOT optional.

| Mode | Purpose | When REQUIRED |
|------|---------|---------------|
| **code-skeptic** | Critical code analysis for problems | REQUIRED at Gate 1 and Gate 3 |
| **code-reviewer** | Thorough review of changes | REQUIRED at Gate 2 |
| **code-simplifier** | Finding simplification opportunities | Use when complexity is identified |
| **debug** | Analyzing potential issues | Use when investigating specific problems |

**Mode Switching Requirements:**
- **MUST** switch to `code-skeptic` mode after each action for critical analysis
- **MUST** switch to `code-reviewer` mode after code changes for thorough review
- **PROHIBITED:** Proceeding without appropriate mode switching at validation gates

---

## Validation Gate Requirements

**MANDATORY VALIDATION GATES:** These gates enforce the planning-criticism-planning cycle. For the full protocol with checklists, see [`ADR-003: Mandatory Validation Protocol`](../../docs/architecture/decisions/adr-003-mandatory-validation-protocol.md).

### Overview

Validation gates are explicit checkpoints that MUST be passed before proceeding to the next stage of development. Each gate requires:
- Mandatory mode switching
- Critical analysis or review
- Documentation of findings
- Explicit confirmation before proceeding

### Gate 1: Pre-Implementation Validation

**Location:** After planning, before any code changes

**Purpose:** Validate the plan before implementation begins

**Requirements:**
- **MUST** switch to `code-skeptic` mode for critical analysis
- **MUST** analyze the plan for potential issues
- **MUST** document findings (even if no issues found)
- **MUST** identify risks and mitigations
- **MUST** receive user confirmation to proceed

**Cannot proceed until:**
- Code-skeptic analysis is complete
- Findings are documented
- User confirms approval

### Gate 2: Post-Implementation Validation

**Location:** After code changes, before completion

**Purpose:** Validate implementation quality before marking complete

**Requirements:**
- **MUST** switch to `code-reviewer` mode for thorough review
- **MUST** verify all changes against the plan
- **MUST** check for unintended side effects
- **MUST** verify tests pass
- **MUST** document review results

**Cannot proceed until:**
- Code-reviewer analysis is complete
- All critical issues are addressed
- Review is documented

### Gate 3: Subtask Completion Validation

**Location:** After each subtask, before merging

**Purpose:** Validate subtask completion and integration

**Requirements:**
- **MUST** switch to `code-skeptic` mode for subtask review
- **MUST** verify subtask meets acceptance criteria
- **MUST** check integration with existing code
- **MUST** verify no regressions
- **MUST** document completion

**Cannot proceed until:**
- Subtask is validated
- Integration is verified
- Documentation is complete

### Prohibition of Immediate Fixes

**MANDATORY RULE:** When errors or issues are detected at any validation gate:

```
1. STOP    - Do NOT make immediate fixes
2. SWITCH  - Switch to appropriate mode (code-skeptic or code-reviewer)
3. ANALYZE - Critically analyze the root cause
4. REPLAN  - Update the plan to address the issue
5. REVALIDATE - Pass through the appropriate gate again
```

**Rationale:** Immediate fixes bypass critical thinking and may introduce new issues or fail to address root causes.

### Validation Gate Checklists

For detailed checklists for each gate, see [`ADR-003: Mandatory Validation Protocol`](../../docs/architecture/decisions/adr-003-mandatory-validation-protocol.md#validation-gate-checklist).

### Integration with Orchestrator Mode

When using orchestrator mode for complex tasks:
- Each subtask MUST pass Gate 3 before merging
- Gate 1 MUST be passed before any implementation
- Gate 2 MUST be passed before final completion

See [`.kilocode/rules-orchestrator/orchestrator.md`](../../.kilocode/rules-orchestrator/orchestrator.md) for orchestrator-specific validation requirements.

---

## Git Workflow

### General Process

```
1. Briefly analyze task (orchestrator mode)
2. Come up with task name
3. Create new branch for task
4. Create additional branches for subtasks
5. After completing subtask — merge into working branch
```

### Creating Branches

**Branch naming format:**
```
feature/short-task-description
bugfix/bug-description
refactor/refactoring-description
docs/documentation-description
```

**Examples:**
```bash
git checkout -b feature/add-state-persistence
git checkout -b bugfix/fix-memory-leak-in-parser
git checkout -b refactor/optimize-event-dispatcher
```

### Commits

**Commit rules:**
1. **Frequency:** Commit as often as possible
2. **History:** Rich and informative commit history
3. **RULE:** Before creating commit, ensure code works OR at least compiles

**Commit message format:**
```
<type>: <short description>

<detailed description (optional)>

<task references (optional)>
```

**Commit types:** `feat`, `fix`, `refactor`, `docs`, `test`, `chore`

**Examples:**
```
feat: add support for nested states

Implemented hierarchical state structure with inheritance.
Added tests in test_state_machine.cpp.

Closes #42
```

```
fix: fix memory leak in ConfigParser

The problem was missing YAML::Node cleanup.
Now using smart pointer.
```

### Completing a Task

```
1. Critical analysis of git diff (use review mode)
2. Check and update documentation
   - Are all affected places documented?
   - Is AGENTS.md updated if project structure/technologies changed?
3. Apply clang-format to all modified code
   - **MANDATORY:** Run `clang-format -i src/**/*.cpp include/**/*.hpp` before completion
   - This ensures consistent code formatting across the project
4. Merge branches (merge all additional branches into main working branch)
5. Working branch does not need to be merged anywhere (programmer will analyze)
```

**MANDATORY CLANG-FORMAT RULE:** Before calling `attempt_completion`, all modes that can modify code MUST run `clang-format -i src/**/*.cpp include/**/*.hpp` to ensure consistent code formatting. This is a non-negotiable final step in task completion.

**Review process:**
```bash
# Show all changes
git diff main..feature/branch-name

# Interactive review
git review main..feature/branch-name
```
