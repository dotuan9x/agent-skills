# Agent Skills

> A curated collection of reusable skills for [Claude Code](https://claude.ai/code) that extend its capabilities with specialized, domain-specific expertise.

Each skill is a self-contained prompt-based tool that activates automatically when relevant — no manual invocation required for most workflows.

## Available Skills

| Skill | Triggers | Description |
|-------|----------|-------------|
| [architecture-designer](architecture-designer/) | `architecture`, `system design`, `ADR`, `microservices`, `scalability`, `database selection`, `how should I structure this?` | Acts as a principal architect with 15+ years of experience. Designs systems, evaluates trade-offs, and documents decisions with ADRs. |
| [bruno-api-client](bruno-api-client/) | `bruno`, `.bru`, `generate collection`, `api testing`, `postman alternative`, `migrate from Postman/Insomnia` | Scans API routes (Express, Next.js, Fastify, etc.) and generates a fully organized [Bruno](https://www.usebruno.com/) collection with environments, auth, and folder structure. |
| [readme-file-generator](readme-file-generator/) | `readme`, `README.md`, `update docs`, `write docs`, `document project`, `my readme is outdated` | Writes comprehensive, well-structured `README.md` files following open-source best practices. |

## Installation

Install individual skills using [skills.sh](https://skills.sh):

```bash
npx skills add dotuan9x/agent-skills/architecture-designer
npx skills add dotuan9x/agent-skills/bruno-api-client
npx skills add dotuan9x/agent-skills/readme-file-generator
```

After installation, the skill activates automatically when you describe a matching task in Claude Code. You can also invoke any skill explicitly:

```
/architecture-designer  Design a microservices system for an e-commerce platform
/bruno-api-client       Generate a Bruno collection from my Express routes in src/routes/
/readme-file-generator  Create a README for this project
```

## How Skills Work

A skill is a directory containing a `SKILL.md` file with:

- **Frontmatter** — `name`, `description` (used to match user intent), and optional metadata like triggers, version, and author.
- **Role definition** — The persona and expertise the agent adopts.
- **Workflow** — Step-by-step instructions the agent follows.
- **Constraints** — What the agent must and must not do.
- **References** (optional) — Supporting documents loaded on demand (e.g., ADR templates, design checklists).

```
my-skill/
├── SKILL.md          # Skill definition
└── references/       # Optional supporting documents
    ├── template.md
    └── checklist.md
```

## Creating Your Own Skills

Use the built-in `skill-creator` skill to build, evaluate, and refine new skills:

```
/skill-creator  Create a skill for writing database migration scripts
```

## License

MIT
