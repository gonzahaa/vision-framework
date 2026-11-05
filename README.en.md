# VISION Framework

[Español](README.md) | English

VISION is a local-first, agent-driven framework for turning project context into a verifiable specification, a sprint plan, and implementable work. Its commands, preconditions, scripts, and workflow state live in the repository.

An AI agent in your IDE reads the project context, produces or updates the planning artifacts, and implements sprint tasks. Node.js scripts handle repeatable operations and keep workflow state consistent. The basic workflow does not require a separate hosted AI service; optional integrations can be added when needed.

## What It Provides

- **Local-first workflow:** project context and generated artifacts stay in your workspace; external integrations are optional.
- **IDE-agnostic commands:** `commands/` is the source of truth, while `scripts/` automates individual steps.
- **Explicit gates:** each stage has preconditions and verifiable outputs.
- **Project flexibility:** supports new projects and reverse-engineering work from an existing codebase.
- **Specialized agent roles:** integrates Agency Agents and setup files for supported IDEs.

## Quick Start

Requirements: Node.js 18 or later and an IDE with an AI agent.

```bash
git clone https://github.com/gonzahaa/vision-framework.git
cd vision-framework
npm install
npm run setup:ide
npm run status
```

Add project documents to `refdocs/`. For reverse engineering, add the existing code to `source-code/`. Then enter `init` in your IDE agent and follow the next action it recommends. VISION workflow commands are entered in the agent chat, not in the terminal.

Check progress at any time with `npm run status`. Start the local status dashboard with `npm run status:web`.

## Workflow

```text
init
  → clarify_brief (optional) → generate_brief
  → clarify_spec_kit (optional) → generate_spec_kit
  → clarify_sprints (optional) → generate_sprints
  → start_sprint --sprint N / continue_sprint
```

Clarification commands are optional and are used when the agent finds ambiguity. Each stage depends on the previous one: the brief informs the specification, the specification informs sprint planning, and the next sprint starts only after the current sprint is complete. Use `next_step` to find the next available action. During `start_sprint` and `continue_sprint`, the agent is expected to implement the tasks, not just summarize them.

## Generated Artifacts

- `spec-kit/input/brief.md`: project goals, scope, users, constraints, and success criteria.
- `spec-kit/input/`: product and technical specifications, data model, API contract, epics, stories, sprint plan, and test plan.
- `planning/sprints/`: sprint goals, stories, tasks, and QA plans.
- `planning/workflow-state.json`: current workflow status and completed stages.

## IDE Setup

`npm run setup:ide` generates IDE entrypoints from the canonical command definitions in `commands/`. Supported setup targets include Cursor, Claude Code, OpenCode, GitHub Copilot, and Antigravity. To inspect the available options, see [commands/README.md](commands/README.md) and [AGENTS.md](AGENTS.md).

## Useful Commands

| Command | Purpose |
|---|---|
| `npm run status` | Show the current workflow stage |
| `npm run status:web` | Start the local workflow dashboard |
| `npm run next_step` | Detect the next available workflow action |
| `npm run test:local` | Run local Node.js tests |
| `npm run lint` | Lint framework scripts |
| `npm run format:check` | Check formatting |
| `npm run check:scripts` | Validate script conventions |

Full guides: [Español](GUIA_COMPLETA_VISION.md) · [English](GUIA_COMPLETA_VISION.en.md). Framework documentation is in [docs/README.md](docs/README.md), and each workflow command is described in `commands/`.

## Repository Layout

```text
commands/       IDE-agnostic command definitions
scripts/        Node.js workflow implementation
bin/            VISION CLI entrypoint
planning/       Current project workflow state and sprint plans
spec-kit/       Specification inputs and templates
refdocs/        Project source documents (ignored by default)
source-code/    Existing code for reverse-engineering projects
agency-agents/  Specialized agent roles
mcp/            Optional local MCP integration
```

## Optional Integrations

The core workflow can run locally. Matrix reporting and local AI CLI integration are optional. See [docs/matrix-reporting.md](docs/matrix-reporting.md) and [docs/mcp-local-setup.md](docs/mcp-local-setup.md) for configuration details.

## License

MIT. See [LICENSE](LICENSE).
