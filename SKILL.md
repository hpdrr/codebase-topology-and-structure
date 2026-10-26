---
name: codebase-topology-and-structure
description: Authoritative skill and operational guidelines for scaffolding, refactoring, evaluating, and generating multi-file application codebases across Python, TypeScript/Node, Go, Java/Kotlin, C#, and Rust.
triggers:
  - Scaffolding new projects or repositories
  - Refactoring multi-file structures or modular boundaries
  - Reviewing architecture, file layout, and package organization
  - Designing domain boundaries, imports, and dependency topology
version: 1.0.0
---

# Codebase Topology & Project Structure Skill

## 1. Core Operating Directives

When planning, scaffolding, evaluating, or refactoring codebases:

1. **Prioritize Vertical Cohesion**: Group code by business capability, feature domain, or bounded context rather than technical tier (controller/service/model). Code changing for the same business reason must reside together on disk.
2. **Enforce Compiler-Level Boundaries**: Rely on language-native visibility controls (e.g., Go `internal/`, Java package-private, Rust `pub(crate)`) over team conventions to prevent cyclic and unauthorized dependencies.
3. **Isolate Transport and Persistence**: Controllers, HTTP routers, RPC handlers, and ORM query builders must remain decoupled from domain logic. Domain models must never import web frameworks or raw SQL drivers.
4. **Maintain Root Cleanliness**: Root directories must only contain repository configuration, build descriptors, container manifests, and documentation. Never scatter loose business logic or one-off helper scripts at the repository root.

---

## 2. Decision Tree & Execution Algorithm

When assigned a project structure or scaffolding task, follow this pipeline:

```text
[Input Prompt / Repository Context]
                 │
                 ▼
     [Identify Target Ecosystem]
                 │
  ┌──────────────┼──────────────┬──────────────┬──────────────┬──────────────┐
  ▼              ▼              ▼              ▼              ▼              ▼
Python       TypeScript         Go          Java/Kotlin       C#            Rust
(src-layout) (AppRouter/Nest)  (cmd/internal) (By-Feature)  (Clean/VSA)   (Workspaces)
  │              │              │              │              │              │
  └──────────────┴──────────────┴──────┬───────┴──────────────┴──────────────┘
                                       │
                                       ▼
                     [Enforce Visibility & Boundaries]
                                       │
                                       ▼
                     [Run Anti-Pattern Verification]
                                       │
                                       ▼
                         [Emit Scaffolding / Code]
```

---

## 3. Universal Architectural Matrix

| Ecosystem | Primary Layout Pattern | Boundary Enforcement Tool | Architectural Standard |
| :--- | :--- | :--- | :--- |
| **Python (FastAPI)** | Domain-Modular `src/` layout | `sys.path` runtime isolation | Explicit APIRouter nesting, dependency injection |
| **Python (Django)** | Service-Selector `apps/` layout | Django settings module separation | Two Scoops methodology; slim models and queries |
| **TypeScript (Next.js)** | App Router Colocation | File-system routing conventions | Route Groups `(group)` and private folders `_components` |
| **TypeScript (NestJS)** | Feature Module Tree | IoC module export boundaries | Domain-Driven Design (DDD) module boundaries |
| **Node.js (Express)** | 3-Tier Layered Architecture | Static import linting / Loaders | Bulletproof Node.js (Loaders, Routes, Services) |
| **Go** | `cmd/` + `internal/` layout | Go compiler package visibility | Official Go module layout; anti-stutter package naming |
| **Java / Kotlin** | Package-by-Feature | JVM package-private visibility | Spring Modulith architectural verification |
| **C# (.NET Core)** | Clean Architecture / VSA | Assembly references & project boundaries | Feature-per-File slices (VSA) or eShopOnWeb Clean |
| **Rust** | Cargo Workspaces (`crates/*`) | `pub`, `pub(crate)` visibility | Domain-driven crates with parallel compilation |

---

## 4. Ecosystem Layout Specifications

### 4.1 Python Standards

#### Rule: Mandatory `src/` Root Layout
Never place importable packages flat at the root. A flat root pollutes `sys.path[0]` with the current working directory, hiding missing package dependencies during local test runs.

#### FastAPI Domain-Modular Layout
```text
├── pyproject.toml
├── uv.lock
├── tests/
│   ├── conftest.py
│   ├── unit/
│   └── integration/
└── src/
    └── app/
        ├── __init__.py
        ├── main.py              # App instantiation, lifecycle hooks, middleware
        ├── dependencies.py      # Global DI callables (e.g., get_db, get_current_user)
        ├── core/
        │   ├── config.py        # pydantic-settings definitions
        │   ├── database.py      # Database engine/session initialization
        │   └── security.py      # Token hashing, crypto primitives
        ├── api/
        │   ├── router.py        # Master APIRouter aggregating versioned routers
        │   └── v1/
        │       ├── api_router.py
        │       └── endpoints/
        │           ├── auth.py
        │           └── users.py
        ├── models/              # Persistence layer (SQLAlchemy/SQLModel)
        ├── schemas/             # Transport validation layer (Pydantic schemas)
        ├── services/            # Pure business workflows (framework-agnostic)
        └── internal/            # Admin tooling, private migration helpers
```

#### Django Service-Selector Pattern
Avoid monolithic settings and bloated Active Record models:
* `services.py`: Write operations, transactional state transitions, multi-model side effects.
* `selectors.py`: Read operations, cache retrieval, projection, and queryset optimizations (`select_related`, `prefetch_related`).

```text
├── manage.py
├── config/
│   ├── settings/
│   │   ├── base.py
│   │   ├── local.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
└── apps/
    ├── common/
    └── orders/
        ├── apps.py
        ├── models.py
        ├── views.py
        ├── urls.py
        ├── services.py
        ├── selectors.py
        └── migrations/
```

---

### 4.2 TypeScript & Node.js Standards

#### Next.js App Router (Colocation & Route Groups)
Rely on directory conventions to colocate business logic, private sub-components, and API handlers.

```text
├── package.json
├── tsconfig.json
├── src/
    ├── components/
    │   └── ui/                  # Reusable, design-system atomic components
    └── app/
        ├── layout.tsx           # Root layout
        ├── page.tsx             # Root landing page
        ├── (auth)/              # Route group: organized without URL impact
        │   ├── layout.tsx
        │   ├── login/page.tsx
        │   └── register/page.tsx
        ├── (dashboard)/
        │   ├── layout.tsx
        │   └── analytics/page.tsx
        ├── _components/         # Private folder: excluded from file-system routing
        │   └── DashboardNav.tsx
        └── api/
            └── webhook/route.ts
```

#### NestJS Enterprise Feature-Module Architecture
Ensure each domain feature encapsulates its own controllers, services, entities, and DTOs. Modules must explicitly declare `exports` to prevent circular and invisible cross-domain leaks.

```text
src/
├── main.ts
├── app.module.ts
├── common/                      # Cross-cutting guards, filters, interceptors
└── modules/
    └── users/
        ├── users.module.ts      # Declares imports, providers, and exports
        ├── users.controller.ts  # HTTP transport validation
        ├── users.service.ts     # Business logic & repository access
        ├── dto/                 # Input/output validation shapes
        └── entities/            # TypeORM/Prisma schema representations
```

#### Express 3-Tier Layered Architecture
```text
src/
├── loaders/                     # DB connection pools, express middleware init
├── api/
│   ├── routes/                  # Schema validation + route delegators
│   └── middlewares/
├── services/                    # Core business logic; zero req/res coupling
├── models/                      # Database schema and queries
└── subscribers/                 # Event emitters and message bus consumers
```

---

### 4.3 Go Standards

#### Core Idioms:
* **No Speculative Directories**: Disallow community templates introducing unnecessary `pkg/`, `deployments/`, or `api/` directories for small-to-medium systems.
* **Anti-Stutter Rule**: Never name packages `models`, `types`, `helpers`, or `utils`. A type must read as `customer.Order`, never `models.CustomerOrder`.
* **Compiler-Enforced Privacy**: Keep domain packages in `internal/`. Code inside `internal/` cannot be imported by external packages.

```text
├── go.mod
├── go.sum
├── cmd/
│   ├── api-server/
│   │   └── main.go              # Entry point for HTTP server
│   └── cli-worker/
│       └── main.go              # Entry point for background consumer
└── internal/
    ├── platform/                # Infrastructure clients (DB, Redis, logger)
    │   ├── database/
    │   └── logger/
    └── customer/                # Domain-specific bounded context
        ├── customer.go          # Core domain types and business logic
        ├── customer_test.go
        ├── repository.go        # Database query definitions
        └── service.go           # Orchestration workflows
```

---

### 4.4 Java & Kotlin Standards

#### Package-by-Feature with Package-Private Encapsulation
Do not group by technical layers (e.g., all controllers in one package, all services in another). Group by feature and leverage default (package-private) visibility so only entry interfaces/DTOs are `public`.

```text
src/main/java/com/company/app/
├── Application.java
├── common/
└── order/
    ├── OrderController.java     # public: Exposed to framework dispatcher
    ├── OrderDto.java            # public: Exposed request/response schema
    ├── OrderService.java        # package-private: Internal orchestrator
    ├── Order.java               # package-private: Internal entity
    └── OrderRepository.java     # package-private: Internal database adapter
```

#### Modular Monolith Verification (Spring Modulith)
When designing modular architectures in Spring Boot, always generate a verification test to guarantee that modules do not violate package boundaries:

```java
@Test
void verifyModularStructure() {
    ApplicationModules.of(Application.class).verify();
}
```

---

### 4.5 C# (.NET) Standards

#### Pattern A: Clean Architecture (eShopOnWeb Topology)
Use when building multi-project solutions with strict domain isolation:
* `Core`: Pure domain entities and interfaces. 0 external project or framework dependencies.
* `Infrastructure`: EF Core, third-party adapters, concrete implementations of `Core` interfaces.
* `WebApi`: Presentation host referencing `Core` and `Infrastructure`.

```text
├── Solution.sln
├── src/
│   ├── Core/                    # Zero external dependencies
│   │   ├── Entities/
│   │   ├── Interfaces/
│   │   └── Exceptions/
│   ├── Infrastructure/          # DB context, API clients
│   │   ├── Data/
│   │   └── Services/
│   └── WebApi/                  # ASP.NET Core endpoints & configuration
│       ├── Program.cs
│       └── Controllers/
└── tests/
```

#### Pattern B: Vertical Slice Architecture (VSA)
Use when organizing by operational use cases instead of horizontal project layers. Group commands, queries, validators, and handlers in single slice files:

```text
src/WebApi/
├── Program.cs
├── Common/
└── Features/
    └── Orders/
        ├── CreateOrder.cs       # Request, CommandHandler, Validator, DB logic
        ├── GetOrderById.cs      # Query, QueryHandler, Output ViewModel
        └── CancelOrder.cs
```

---

### 4.6 Rust Standards

#### Testing Topology Rules:
* **In-Tree Unit Tests**: Place unit tests inside the implementation file using `#[cfg(test)]` modules to allow white-box testing of private fields and functions.
* **Out-of-Tree Integration Tests**: Place public API integration tests inside a top-level `tests/` directory.

#### Cargo Workspaces Pattern:
Decompose large Rust applications into separate crates compiled in parallel:

```text
├── Cargo.toml                   # Root workspace manifest
├── Cargo.lock
├── crates/
│   ├── api/
│   │   ├── Cargo.toml
│   │   └── src/main.rs          # HTTP/gRPC binary crate
│   ├── core/
│   │   ├── Cargo.toml
│   │   └── src/lib.rs           # Pure domain definitions
│   └── persistence/
│       ├── Cargo.toml
│       └── src/lib.rs           # Database & external storage
└── tests/                       # End-to-end multi-crate integration tests
```

---

## 5. Architectural Anti-Patterns Checklist

Before returning any suggested file structure, audit the layout against these failure points:

| Anti-Pattern | Description | Remediation |
| :--- | :--- | :--- |
| **The Junk Drawer** | Creating generic `utils/`, `helpers/`, `common/` directories containing unrelated functions. | Colocate helper logic directly within the consuming domain module, or create domain-specific packages (e.g., `encoding/csv/`, `cryptography/`). |
| **Persistence Leaks** | Exposing ORM models (SQLAlchemy, Prisma, EF Core) directly to HTTP responses or external APIs. | Introduce explicit Data Transfer Objects (DTOs) or Pydantic schemas in the transport layer. |
| **Bidirectional Coupling** | Module A imports Module B while Module B imports Module A. | Decouple using domain events, dependency inversion (interfaces), or extract shared contracts to an independent leaf package. |
| **Excessive Surface Area** | Marking all classes, functions, and structs `public` or exporting everything. | Restrict visibility to package-private, internal, or crate-level (`pub(crate)`) by default. Expose only explicit entry points. |
| **Flat Python Roots** | Putting application packages alongside configuration files at the repo root. | Relocate code into `src/<package_name>/` to ensure clean environment separation and proper packaging tests. |
| **Package Stuttering** | Designing Go packages such that usage reads as `models.UserModel` or `utils.StringUtils`. | Rename packages according to their domain context: `user.Model` or `stringx.Format`. |

---

## 6. Verification Steps for Agents

1. **Check Workspace Boundaries**: Are top-level files strictly limited to project manifests (`pyproject.toml`, `package.json`, `go.mod`, `Cargo.toml`), documentation, and CI configurations?
2. **Trace Dependency Direction**: Does the dependency arrow point inward toward the domain logic? (Transport $\rightarrow$ Service/Application $\rightarrow$ Domain; Infrastructure $\rightarrow$ Domain interfaces).
3. **Verify Visibility Modifiers**: Are package-private/internal scoping tools active to prevent arbitrary cross-imports between unrelated domain features?
4. **Audit Separation of Reads and Writes**: For complex data-driven architectures (like Django), are query optimizations isolated into selectors and state mutations isolated into services?