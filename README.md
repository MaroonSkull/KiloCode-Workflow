# KiloCode Workflow

Experimental KiloCode Workflow template for development with AI assistants based on my own experience.

## 📋 Description

**KiloCode Workflow** is a ready-to-use set of rules, settings, and templates for integrating AI assistants (Kilo Code, Claude, GitHub Copilot, and others) into the software development process. The pipeline is based on real experience using the `.kilocode` directory from the FSMConfig project and adapted for use in any project.

> **Note:** This is a template that requires adaptation to your specific project. The `.kilocode` files contain rules and instructions for AI assistants. Copy the `.kilocode` directory from the repository and adapt it to your project.

The pipeline ensures:
- 🎯 **Unified development standards** for all team members (humans and AI)
- 🔄 **Mandatory validation cycle** (planning → criticism → implementation → review)
- 📚 **Automatic documentation generation** with AI assistance
- 🧪 **Code quality** through linters and mandatory checks
- 🧠 **Knowledge Graph** for preserving project context between sessions

## ✨ Key Features

### 🎓 Multiple Operation Modes
- **Architect** — architecture planning and design
- **Ask** — explanations, documentation, and technical answers
- **Code** — writing and modifying code
- **Code Skeptic** — critical code analysis for finding issues
- **Code Reviewer** — thorough review of changes
- **Code Simplifier** — refactoring to simplify code
- **Debug** — diagnosing and fixing errors
- **Documentation Specialist** — creating documentation
- **Orchestrator** — coordinating complex multi-step tasks
- **Review** — reviewing code changes
- **Test Engineer** — writing tests

### 🚪 Validation Gates
Mandatory checkpoints for quality control:
- **Gate 1**: Pre-Implementation Validation (before writing code)
- **Gate 2**: Post-Implementation Validation (after changes)
- **Gate 3**: Subtask Completion Validation (before merging)

### 🔧 Tool Integration
- **MCP servers**: Context7 (library documentation), Memory (knowledge graph), GitHub, Web Search
- **Linters**: clang-tidy, clang-format, cppcheck, eslint, ruff, mypy
- **CI/CD**: GitHub Actions, GitLab CI
- **IDE**: VS Code, CLion

## 📁 .kilocode Directory Structure

```
.kilocode/
├── rules/                     # Common rules for all modes
│   └── common.md
├── rules-architect/           # Rules for Architect mode
│   └── architect.md
├── rules-ask/                 # Rules for Ask mode
│   └── ask.md
├── rules-code/                # Rules for Code mode
│   └── code.md
├── rules-code-reviewer/       # Rules for Code Reviewer mode
│   └── code-reviewer.md
├── rules-code-simplifier/     # Rules for Code Simplifier mode
│   └── code-simplifier.md
├── rules-code-skeptic/        # Rules for Code Skeptic mode
│   └── code-skeptic.md
├── rules-debug/               # Rules for Debug mode
│   └── debug.md
├── rules-docs-specialist/     # Rules for Documentation Specialist mode
│   └── docs-specialist.md
├── rules-orchestrator/        # Rules for Orchestrator mode
│   └── orchestrator.md
├── rules-review/              # Rules for Review mode
│   └── review.md
├── rules-test-engineer/       # Rules for Test Engineer mode
│   └── test-engineer.md
├── setup-script               # Setup script (protected)
└── worktrees/                 # Worktrees directory (protected)
```

> **Learn more:** Each file contains specific rules for the corresponding mode. Full file contents are in the repository and should be copied to your project.

## 🚀 Quick Start

### Step 1: Install MCP Servers

Before starting, install the required MCP servers:

#### Context7 Server (for library documentation)

```bash
# Install via npm
npx -y @upstash/context7-mcp

# Or globally
npm install -g @upstash/context7-mcp
```

#### Memory Server (for knowledge graph)

```bash
# Install via Docker
docker run -i -v claude-memory:/app/dist --rm mcp/memory

# Or locally
npm install -g @modelcontextprotocol/server-memory
```

#### GitHub MCP Server

```bash
# Install via Docker
docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN \
    -e GITHUB_TOOLSETS -e GITHUB_READ_ONLY \
    ghcr.io/github/github-mcp-server

# Requires GitHub Personal Access Token with repo permissions
```

#### Verify Installation

```bash
# Check that MCP servers are available
# In Claude Desktop: Settings → MCP Servers
# Servers should appear: context7, memory, github
```

### Step 2: Clone and Configure

```bash
# Clone the repository
git clone https://github.com/MaroonSkull/KiloCode-Workflow.git
cd KiloCode-Workflow

# Copy .kilocode to your project
cp -r .kilocode /path/to/your/project/

# Copy the AGENTS.md template
cp AGENTS.md.template /path/to/your/project/AGENTS.md
```

### Step 3: Adapt to Your Project

Open `AGENTS.md` and replace the placeholders:

```markdown
# Replace {PROJECT_NAME} with your project name
# Replace {LANGUAGE} with the programming language
# Replace {BUILD_SYSTEM} with the build system
# And so on...
```

**Example for a Python project:**
```markdown
## Project Overview

### Purpose

**MyAwesomeProject** is a REST API for task management.
The library provides CRUD operations and integration with external services.

### Python Standard and Features

- **Standard:** Python 3.11+
- **Features:**
  - Type hints
  - Async/await
  - Dataclasses
```

### Step 4: Configure Linters (Optional)

If you use C/C++:

```bash
# Copy configuration files (if available)
cp .clang-tidy .clang-format .cppcheck /path/to/your/project/
```

For Python:
```bash
# Create pyproject.toml with ruff, mypy, black configuration
```

For JavaScript/TypeScript:
```bash
# Create .eslintrc.js and .prettierrc
```

### Step 5: Get Started

```bash
# Create a branch for the task
git checkout -b feature/my-first-ai-task

# Launch AI assistant in Code mode
# Prompt: "Create a function to parse JSON"
```

## 📋 Project Requirements

To use KiloCode Workflow, your project must meet the minimum requirements:

| Requirement | Description | Required |
|-------------|-------------|----------|
| **Version control** | Git, GitHub, GitLab, or similar | ✅ Yes |
| **Documentation directory** | `docs/` in project root | ✅ Yes |
| **README.md** | Project description in root | ✅ Yes |
| **AGENTS.md** | Project description for AI (from template) | ✅ Yes |
| **Linters** | Any (clang-tidy, eslint, pylint...) | ⚠️ Recommended |
| **CI/CD** | GitHub Actions, GitLab CI, Jenkins... | ⚠️ Recommended |
| **Tests** | Unit tests, integration tests | ⚠️ Recommended |

## 💡 Usage Examples

### Example 1: Creating a New Feature (Detailed)

```bash
# 1. Create a branch
git checkout -b feature/add-user-auth

# 2. Launch AI in Architect mode
# Prompt: "Design a user authentication system"
#
# Expected result from AI:
# - Create file docs/architecture/decisions/adr-001-auth.md
# - Describe architecture (JWT tokens, refresh tokens)
# - Propose class structure (AuthService, TokenManager, UserController)
# - Specify dependencies (bcrypt, jsonwebtoken)
# - Create component diagram

# 3. Switch to Code mode
# Prompt: "Implement authentication according to ADR-001"
#
# Expected result from AI:
# - Create files: src/auth/AuthService.ts, src/auth/TokenManager.ts
# - Implement methods: login(), register(), refreshToken()
# - Add input validation
# - Create unit tests in tests/auth.test.ts
# - Pass Gate 1: architecture analysis
# - Pass Gate 2: code review after implementation

# 4. Switch to Code Skeptic mode
# Prompt: "Check the implementation for potential issues"
#
# Expected result from AI:
# - Check for memory leaks
# - Check for race conditions
# - Check for SQL injection
# - Check for improper error handling
# - Suggest improvements

# 5. Apply formatting
clang-format -i src/**/*.cpp include/**/*.hpp  # for C++
# or
prettier --write "src/**/*.{js,ts}"  # for JavaScript/TypeScript
# or
black src/  # for Python

# 6. Run tests
npm test  # or pytest, ctest --output-on-failure

# 7. Commit changes
git add .
git commit -m "feat: add user authentication

Implemented JWT-based authentication with refresh tokens.
Added AuthService, TokenManager, and UserController.
Created unit tests with 90% coverage.

Closes #42"
```

### Example 2: Code Refactoring (Detailed)

```bash
# 1. Create a branch
git checkout -b refactor/optimize-data-processing

# 2. Launch AI in Code Simplifier mode
# Prompt: "Simplify the processUserData function in src/user.py"
#
# Expected result from AI:
# - Analyze the function (e.g., 200 lines)
# - Identify code duplication
# - Propose extracting helper functions
# - Simplify nested conditions
# - Apply Strategy pattern for different processing types
#
# Example simplification:
# Before:
# def processUserData(data):
#     if data['type'] == 'A':
#         # 50 lines of code
#     elif data['type'] == 'B':
#         # 50 lines of code (almost the same operations)
#     elif data['type'] == 'C':
#         # 50 lines of code (duplication again)
#
# After:
# def processUserData(data):
#     processor = getProcessorForType(data['type'])
#     return processor.process(data)
#
# def getProcessorForType(type):
#     processors = {
#         'A': TypeAProcessor(),
#         'B': TypeBProcessor(),
#         'C': TypeCProcessor(),
#     }
#     return processors.get(type, DefaultProcessor())

# 3. Launch AI in Code Skeptic mode
# Prompt: "Check the refactoring for potential issues"
#
# Expected result from AI:
# - Check that logic hasn't changed
# - Check for edge cases (None, empty data)
# - Check performance
# - Check backward compatibility

# 4. Run tests
pytest tests/ -v

# Expected result:
# tests/test_user.py::test_process_type_a PASSED
# tests/test_user.py::test_process_type_b PASSED
# tests/test_user.py::test_process_type_c PASSED
# tests/test_user.py::test_process_invalid_type PASSED
# ================= 4 passed in 0.5s =================

# 5. Check coverage
pytest --cov=src --cov-report=html

# Expected result:
# Coverage: 95% (was 85%, improved!)

# 6. Commit
git add .
git commit -m "refactor: simplify user data processing

Extracted type-specific processors using Strategy pattern.
Reduced code duplication from 150 to 50 lines.
Improved test coverage from 85% to 95%."
```

### Example 3: Using MCP Memory Server

```bash
# AI automatically creates entities in the knowledge graph

# Example: after working with the User class
# AI will create:
# - Entity: "User" (type: class)
#   - Observations:
#     - "User class handles authentication"
#     - "User has 5 public methods: login, register, updateProfile, changePassword, delete"
#     - "User uses bcrypt for password hashing"
#     - "User depends on TokenManager for JWT operations"
# - Entity: "user.py" (type: file)
#   - Observations:
#     - "Contains User class implementation"
#     - "Located in src/auth/ directory"
#     - "200 lines of code"
# - Entity: "bcrypt" (type: library)
#   - Observations:
#     - "Used for password hashing"
#     - "Version 4.0.1"
# - Relations:
#   - User -> uses -> bcrypt
#   - User -> part_of -> user.py
#   - User -> depends_on -> TokenManager

# In the next session, AI will be able to:
# 1. Search: "Find all classes related to authentication"
#    AI will answer: "Found: User, TokenManager, AuthService"
#
# 2. Context: AI will immediately understand the project structure
#    "The project has a User class in src/auth/ that uses bcrypt"
#
# 3. Dependency analysis: "What will break if we change bcrypt?"
#    AI will answer: "User class depends on bcrypt. Also affects TokenManager"

# Example query to Memory Server:
# (AI does this automatically, but can be done manually)
# mcp--memory--search_nodes(query="authentication")
# Returns: User, TokenManager, AuthService, bcrypt

# mcp--memory--open_nodes(names=["User"])
# Returns detailed information about the User class
```

## 🔧 Troubleshooting

### MCP Servers Not Connecting

**Problem:** AI doesn't see MCP servers (Context7, Memory, GitHub)

**Solution:**
1. Check that servers are installed:
   ```bash
   which context7-mcp  # for Context7
   docker ps | grep memory  # for Memory Server
   ```

2. Check Claude Desktop configuration:
   - File: `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS)
   - Or: `%APPDATA%\Claude\claude_desktop_config.json` (Windows)
   ```json
   {
     "mcpServers": {
       "context7": {
         "command": "npx",
         "args": ["-y", "@upstash/context7-mcp"]
       },
       "memory": {
         "command": "docker",
         "args": ["run", "-i", "-v", "claude-memory:/app/dist", "--rm", "mcp/memory"]
       }
     }
   }
   ```

3. Restart Claude Desktop

### AI Not Creating Files in Correct Directories

**Problem:** AI creates files not in `docs/.wip/`, but in the project root

**Solution:**
1. Check that `.kilocode/rules/common.md` is copied correctly
2. Ensure `AGENTS.md` specifies the correct project structure
3. Give explicit instruction to AI: "Create file in `docs/.wip/feature/...`"

### Linters Reporting Errors

**Problem:** `clang-tidy` or `eslint` report errors after AI changes

**Solution:**
1. Run linter with `--fix` flag for auto-fix:
   ```bash
   clang-tidy -p build src/**/*.cpp --fix
   eslint src/ --fix
   ```

2. If errors remain, ask AI to fix:
   ```
   "Run clang-tidy and fix all found issues"
   ```

### Knowledge Graph Empty

**Problem:** Memory Server contains no project data

**Solution:**
1. Ask AI to create entities:
   ```
   "Analyze the project and create entities in the knowledge graph for all classes"
   ```

2. Check contents:
   ```
   "Show all entities in the knowledge graph"
   ```

## 🤝 Contributing

We welcome contributions to the development of KiloCode Workflow!

### How to Contribute

1. **Fork the repository**
2. **Create a branch**: `git checkout -b feature/improve-pipeline`
3. **Make changes**
4. **Run validation**: use AI in Code Skeptic mode for verification
5. **Create a Pull Request**

### Contribution Areas

- 🌍 **New language support**: add rules for Python, Rust, Go...
- 🔧 **New skills**: create reusable skills
- 📚 **Documentation**: improve examples and guides
- 🧪 **Tests**: add tests to verify rule correctness
- 🎨 **Templates**: create templates for different project types

## 📖 Additional Resources

### Project Documentation

- [`AGENTS.md.template`](AGENTS.md.template) — template for AGENTS.md

### External Resources

- [Model Context Protocol](https://modelcontextprotocol.io/) — MCP specification
- [Claude Documentation](https://docs.anthropic.com/) — Claude documentation
- [GitHub Copilot](https://github.com/features/copilot) — GitHub Copilot
- [Context7](https://context7.dev/) — Context7 documentation

## 📄 License

2025 MaroonSkull

GPL-3.0 License — see [LICENSE](LICENSE) file

## 🙏 Acknowledgments

KiloCode Workflow was created based on development experience from the [FSMConfig](https://github.com/MaroonSkull/FSMConfig) project and inspired by best practices in AI-assisted development.

---

**Created with ❤️ to improve AI-assisted development**
