# Codebase Topology & Structure Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skill Version](https://img.shields.io/badge/skill-v1.0.0-green.svg)](SKILL.md)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

An authoritative [Agent Skill](https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview) providing operational guidelines, decision trees, and layout patterns for scaffolding, refactoring, evaluating, and generating multi-file application codebases.

Covers **Python**, **TypeScript / Node.js**, **Go**, **Java / Kotlin**, **C# (.NET)**, and **Rust**.

---

## What It Does

When an AI coding assistant designs, evaluates, or refactors a repository, this skill enforces industry-proven structural patterns rather than generic flat layouts:

1. **Prioritizes Vertical Cohesion**: Groups code by business capability and bounded contexts rather than horizontal technical tiers (`controllers/`, `services/`, `models/`).
2. **Compiler-Enforced Privacy**: Employs language-native visibility controls (Go `internal/`, Java package-private, Rust `pub(crate)`) to eliminate cyclic and unauthorized dependencies.
3. **Decoupled Transport and Persistence**: Isolates HTTP/RPC handlers, ORM models, and database drivers from pure domain logic.
4. **Root Cleanliness**: Restricts root folders to build descriptors, manifests, and documentation; bans flat business logic.
5. **Anti-Pattern Guard**: Proactively audits against common architectural traps (e.g., the Junk Drawer `utils/`, Persistence Leaks, Circular Imports, and Go Package Stuttering).

---

## Architectural Matrix

| Ecosystem | Primary Layout Pattern | Boundary Enforcement | Standards |
| :--- | :--- | :--- | :--- |
| **Python (FastAPI)** | Domain-Modular `src/` layout | `sys.path` runtime isolation | Explicit APIRouter nesting, dependency injection |
| **Python (Django)** | Service-Selector `apps/` layout | Django settings separation | Two Scoops methodology; slim models and queries |
| **TypeScript (Next.js)** | App Router Colocation | File-system routing conventions | Route Groups `(group)` and private folders `_components` |
| **TypeScript (NestJS)** | Feature Module Tree | IoC module export boundaries | Domain-Driven Design (DDD) module boundaries |
| **Node.js (Express)** | 3-Tier Layered Architecture | Static import linting / Loaders | Bulletproof Node.js (Loaders, Routes, Services) |
| **Go** | `cmd/` + `internal/` layout | Go compiler package visibility | Official Go module layout; anti-stutter package naming |
| **Java / Kotlin** | Package-by-Feature | JVM package-private visibility | Spring Modulith architectural verification |
| **C# (.NET Core)** | Clean Architecture / VSA | Assembly references & project boundaries | Feature-per-File slices (VSA) or eShopOnWeb Clean |
| **Rust** | Cargo Workspaces (`crates/*`) | `pub`, `pub(crate)` visibility | Domain-driven crates with parallel compilation |

---

## Installation

### Method 1: Ask Your Agent (Recommended)

Paste this into your AI coding assistant (Gemini CLI, Antigravity, Claude Code, Cursor, Codex):

```text
# Clone or copy the directory into your skills folder, and verify it is registered.
Install the codebase-topology-and-structure skill from https://github.com/hpdrr/codebase-topology-and-structure.git
```

Your agent will inspect its own configuration and register the skill.

### Method 2: Manual Installation

Clone this repository into your agent's skill directory:

#### For Antigravity / Gemini CLI:
```bash
# Global installation (available across all workspaces)
git clone https://github.com/hpdrr/codebase-topology-and-structure.git ~/.gemini/config/skills/codebase-topology-and-structure
```

#### For Claude Code:
```bash
# Workspace only
git clone https://github.com/hpdrr/codebase-topology-and-structure.git your-project/.claude/skills/codebase-topology-and-structure

# User-level (all workspaces)
git clone https://github.com/hpdrr/codebase-topology-and-structure.git ~/.claude/skills/codebase-topology-and-structure
```

#### For Other Agent Frameworks:
Copy `SKILL.md` or clone the repository into your agent's configured skills path (such as `.agent/skills/` or `~/.agents/skills/`).

---

## Usage Examples

Once installed, the skill activates automatically when you discuss repository layout, architecture, scaffolding, or modular refactoring. You can also trigger it explicitly:

### 1. Scaffolding New Systems
> "Scaffold a new production-ready FastAPI microservice for payment handling with database models and authentication. Follow the codebase-topology skill."

### 2. Layout Refactoring
> "Refactor this Go web service. Eliminate package stuttering and move non-exported application packages into internal/."

### 3. Architecture & Boundary Audit
> "Audit our TypeScript repository structure against the architectural anti-patterns checklist in the codebase-topology skill."

---

## Repository Contents

```text
codebase-topology-and-structure/
├── .github/
│   ├── workflows/
│   │   └── validate.yml          # Frontmatter and Markdown validation CI
│   └── pull_request_template.md  # PR checklist
├── .gitignore                    # OS and editor ignores
├── CONTRIBUTING.md               # Guidelines for expanding ecosystem patterns
├── LICENSE                       # MIT License
├── README.md                     # Documentation and quickstart
└── SKILL.md                      # Core skill instruction definition and decision trees
```

---

## Contributing

We welcome contributions expanding patterns for additional frameworks, language tooling, or modular verification tests! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## License

This project is licensed under the [MIT License](LICENSE).
