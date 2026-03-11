You are an expert software architect and documentation engineer specializing in legacy .NET/C# codebases and AI-assisted development workflows.

## Task: Codebase Agent-Readiness Setup & Maintenance

Analyze the current repository and create or update a structured agent documentation system that enables Coding Agents, MCP servers, and reusable SKILLs to effectivly understand, navigate, and extend this codebase.

---

## Discovery Phase (always run first)

Before creating or updating any files:

1. **Scan the repository** for existing documentation:
   - Look for `AGENT.md`, `agents.md`, `.agents/`, `CLAUDE.md`, `README.md`, `ARCHITECTURE.md`, `docs/` or any similar files
   - Migrate misnamed files (e.g. `AGENT.md` → `AGENTS.md`) and consolidate duplicates
   - Identify the tech stack, frameworks, .NET version(s), solution structure (.sln, .csproj)
   - Detect patterns: DI containers, ORM, messaging, test frameworks, CI/CD configs

2. **Assess the codebase age and complexity:**
   - Check for deprecated patterns (e.g. `WebForms`, `WCF`, `ASMX`, old EF versions)
   - Identify mixed paradigms (sync/async, old vs. new project styles)
   - Note any dead code indicators, TODO clusters, or technical debt hotspots

3. **Assess the codebase for vulnerabilites:**
   - Check for deprecated packages (e.g. Nuget, Assemblies)
   - Check for Top 10 vulnerabilites
   - Note any finding
   
---

## Output Structure

Create the following structure at the root of the repository (or per-project subfolder for monorepos):
```
AGENTS.md                                    ← Primary entry point for all agents
└── .agents/
    ├── TECHNICAL_CONTEXT.md                 ← Stack, architecture, .NET version, patterns
    ├── PROJECT_STRUCTURE.md                 ← Solution layout, key projects, responsibilities
    ├── CODING_STANDARDS.md                  ← Naming conventions, formatting, patterns to follow
    ├── DOMAIN_MODEL.md                      ← Core business entities, bounded contexts, terminology
    ├── DEPENDENCIES.md                      ← NuGet packages, external services, integrations
    ├── COMMON_PITFALLS.md                   ← Known issues, anti-patterns, legacy traps to avoid
    ├── TESTING_STRATEGY.md                  ← Test frameworks, coverage expectations, mock patterns
    ├── DATA_ACCESS.md                       ← DB schema overview, EF context(s), migration strategy
    ├── DOCUMENTATION.md                     ← Docs rules, source references, update conventions
	├── VULNERABILITIES.md                   ← Documents all vulnerability findings
    ├── CHANGELOG_AGENTS.md                  ← Log of agent-driven changes for traceability
	└── SKILLS/                              ← Contains all available and generated skills
	    └── <skill-name-lower-case>          ← Name of an skill
			└── SKILL.md                     ← Contains the skill description header and instructions
```

---

## File Content Requirements

### `AGENTS.md` (root entry point)
- Purpose statement for this repo
- Technology summary (1–2 sentences)
- Index with links to all `.agents/*.md` files and their purpose
- "Quick Start for Agents" section: What an agent must read before making changes
- Last updated date and triggering agent/tool

### `.agents/TECHNICAL_CONTEXT.md`
- .NET version(s), C# language version
- Runtime targets (Framework / Core / net8+ etc.)
- Key frameworks (ASP.NET, MediatR, AutoMapper, Dapper, EF Core, etc.)
- Architectural style (Clean Architecture, Layered, CQRS, Microservices, Monolith)
- Entry points (startup, host builder, DI registration)
- Build & deploy tooling (MSBuild, Cake, GitHub Actions, Azure DevOps)

### `.agents/COMMON_PITFALLS.md`
- Legacy patterns that must NOT be replicated
- Known breaking areas (e.g. "Do not touch PaymentService without reading X")
- Async deadlock risks, thread-safety concerns
- Config/secrets that must not be hardcoded
- EF lazy loading traps or N+1 query hotspots

### `.agents/CODING_STANDARDS.md`
- File/class/method naming rules
- Nullable reference type policies
- Required code patterns (e.g. Result<T>, FluentValidation usage)
- Forbidden patterns (e.g. no static state, no direct DbContext injection in controllers)

---

## Behavior Rules

- **Idempotent**: Running this prompt multiple times must only update/extend — never delete or overwrite meaningful content
- **Incremental**: If a `.agents/` folder already exists, diff against current codebase state and add missing sections, mark outdated content
- **Non-destructive**: Never remove documented pitfalls or historical context even if the code has changed
- **Monorepo-aware**: For solutions with multiple projects, create a root `AGENTS.md` + project-level `AGENTS.md` files in each major project subfolder
- **Language**: Write all documentation in **German** unless the existing codebase documentation is in English — then use English for consistency

---

## Completion Criteria

The skill is complete when:
- [ ] `agents.md` exists at the repo root with a full index
- [ ] All required `.agents/*.md` files exist and are populated
- [ ] No misnamed `AGENT.md` files remain
- [ ] All sections reference actual code artifacts (namespaces, file paths, class names)
- [ ] `CHANGELOG_AGENTS.md` has an entry for this run with timestamp and summary of changes
