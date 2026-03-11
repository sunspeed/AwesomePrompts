# AwesomePrompts

A curated collection of reusable AI prompts designed to supercharge your development workflows — from codebase documentation to agent-readiness setup.

## 📋 Prompts

### [Agent Readiness Prompt](./COMMANDS/agent-readiness-prompt.md)

**Purpose:** Sets up and maintains a structured agent documentation system for software repositories — enabling Coding Agents, MCP servers, and reusable SKILLs to effectively understand, navigate, and extend a codebase.

**Best suited for:** Legacy .NET/C# codebases, but adaptable to any tech stack.

**What it does:**
- Scans the repository for existing documentation and consolidates it
- Assesses the codebase for deprecated patterns, technical debt, and security vulnerabilities
- Creates a standardized `.agents/` folder structure with documentation files covering architecture, coding standards, domain model, dependencies, known pitfalls, testing strategy, and more
- Generates an `AGENTS.md` root entry point that indexes all agent documentation

**Output structure created:**
```
AGENTS.md
└── .agents/
    ├── TECHNICAL_CONTEXT.md
    ├── PROJECT_STRUCTURE.md
    ├── CODING_STANDARDS.md
    ├── DOMAIN_MODEL.md
    ├── DEPENDENCIES.md
    ├── COMMON_PITFALLS.md
    ├── TESTING_STRATEGY.md
    ├── DATA_ACCESS.md
    ├── DOCUMENTATION.md
    ├── VULNERABILITIES.md
    ├── CHANGELOG_AGENTS.md
    └── SKILLS/
```

---

## 🚀 How to Use

Copy the desired prompt into your AI agent, coding assistant, or MCP-enabled tool and run it against your repository. Each prompt is designed to be **idempotent** — safe to run multiple times without overwriting meaningful content.

## 📄 License

MIT License — see [LICENSE](./LICENSE) for details.

© 2026 sunspeed
