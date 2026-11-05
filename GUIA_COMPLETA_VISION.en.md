# Complete Guide to VISION Framework
**For first-time users of the framework**

[Español](GUIA_COMPLETA_VISION.md) | English

---

## 🎯 What Is VISION Framework?

**VISION Framework** is a complete system for managing software development in a structured way, from the initial idea through implementation in sprints. Unlike other methodologies, VISION uses **agent commands** that you enter directly in your IDE's agent window (Cursor, Claude Code, GitHub Copilot, and others). The AI agent orchestrates the process for you.

### Core Principles

- **Local-first:** Everything runs on your machine without mandatory external services.
- **Agent-driven:** The IDE agent runs commands, reads files, generates artifacts, and writes code.
- **Template-based:** Uses professional GitHub templates (Spec Kit) and specialized roles (Agency Agents).
- **Structured workflow:** Every phase has clear preconditions and produces verifiable artifacts.

---

## 📁 Project Structure

When you initialize a VISION project, its structure includes:

```text
vision-framework/
├── refdocs/                    ← YOUR input documents (PDFs, Markdown, images, etc.)
├── source-code/                ← Existing code (for reverse engineering)
├── spec-kit/
│   ├── input/                  ← Generated artifacts (brief, PRD, specs, etc.)
│   ├── templates/              ← Official GitHub Spec Kit templates
│   └── upstream/               ← Cloned repository (gitignored)
├── agency-agents/
│   ├── agents/                 ← Agent roles by specialty
│   │   ├── engineering/        ← Frontend, Backend, DevOps, etc.
│   │   ├── design/             ← UI, UX, Research
│   │   ├── testing/            ← QA, Performance, Accessibility
│   │   ├── product/            ← PM, Sprint Prioritizer
│   │   └── project-management/ ← Project Manager, Operations
│   └── upstream/               ← Cloned repository (gitignored)
├── planning/
│   ├── workflow-state.json     ← Central workflow state (DO NOT edit manually)
│   ├── clarifications/         ← Answers from clarification sessions
│   └── sprints/                ← Detailed plans for each sprint
│       ├── sprint-01/
│       │   ├── sprint-goal.md  ← Sprint objective
│       │   ├── stories.md      ← Stories assigned to the sprint
│       │   ├── tasks.md        ← Tasks with owner, status, and points
│       │   └── qa-plan.md      ← Acceptance criteria and QA
│       └── sprint-02/
│           └── ...
├── commands/                   ← Command definitions (source of truth)
├── scripts/                    ← Node.js implementation of each command
├── AGENTS.md                   ← Quick command reference
├── CLAUDE.md                   ← Claude Code instructions
├── .cursor/                    ← Cursor configuration
├── .claude/                    ← Claude Code configuration
├── agent-roles.json            ← Role mapping by area
└── .env                        ← Local configuration (optional)
```

---

## 🔄 Complete Workflow

VISION follows a sequential workflow with validation at every stage:

```text
📋 PHASE 1: INITIALIZATION
   └─ init

📝 PHASE 2: BRIEF (Context document)
   ├─ clarify_brief (optional when there are ambiguities)
   └─ generate_brief

📐 PHASE 3: TECHNICAL SPECIFICATION
   ├─ clarify_spec_kit (optional when the brief is ambiguous)
   └─ generate_spec_kit → Generates 8 artifacts

🗓️ PHASE 4: SPRINT PLANNING
   ├─ clarify_sprints (optional when the spec is ambiguous)
   └─ generate_sprints → Generates the full plan

🚀 PHASE 5: EXECUTION
   ├─ start_sprint --sprint 1 → IMPLEMENTS the sprint code
   ├─ continue_sprint          → Resumes an active sprint with pending tasks
   └─ start_sprint --sprint 2 → Next sprint (automatically closes the previous one)
```

### The Golden Rule of Sequencing

**Each stage must be complete before the next one begins.** You cannot skip stages:
- You cannot generate the Spec Kit without a brief.
- You cannot generate sprints without all 8 Spec Kit artifacts.
- You cannot start sprint 2 until every task in sprint 1 is marked `done`.

---

## 🛠️ Available Tools

### 1. **Core Commands** (Main workflow)

#### `init`
**What it does:** Initializes the project, resets workflow state, and configures the environment.

**How to use it:**
```
init
```

**The agent will ask you:**
1. Is this reverse engineering (are you starting from existing code)?
   - `yes` → You will need files in `source-code/`.
   - `no` → You only need documents in `refdocs/`.
2. Should Spec Kit be downloaded or updated? (GitHub templates)
3. Should Agency Agents be downloaded or updated? (agent roles)

**What it generates:**
- Resets `planning/workflow-state.json` to `created`.
- Downloads templates if you requested them.
- Configures the project mode (new project vs. reverse engineering).

**Next step:** `generate_brief` (after placing your documents in `refdocs/`).

---

#### `generate_brief`
**What it does:** The agent reads all your input documents and writes a structured executive brief.

**Preconditions:**
- The project has been initialized (`init` has run).
- At least one file exists in `refdocs/`.
- For reverse engineering, at least one file exists in `source-code/`.

**How it works:**
1. The agent reads `planning/workflow-state.json` to determine whether this is reverse engineering.
2. It reads ALL files in `refdocs/` (PDFs, Markdown, JSON, YAML, and other supported formats).
3. For reverse engineering, it also reads the code in `source-code/`.
4. If `planning/clarifications/brief.json` exists, it incorporates those answers.
5. **The agent writes** the brief with these required sections:
   - Executive Summary
   - Context
   - Goals
   - Target Users / Roles
   - Scope — In / Out
   - Functional Requirements Summary
   - Technical Stack & Constraints
   - Success Criteria
   - Open Questions / Risks
   - Input Sources
6. It saves the result to `spec-kit/input/brief.md`.
7. It runs `node scripts/generate-brief.js --agent-written` to record the event.

**What it generates:**
- `spec-kit/input/brief.md` (a document of 400+ words).

**Next step:** `generate_spec_kit` (you can edit the brief first).

---

#### `generate_spec_kit`
**What it does:** Generates the 8 technical specification artifacts from the brief.

**Preconditions:**
- `spec-kit/input/brief.md` exists (created by `generate_brief`).
- A product PRD is available (in `refdocs/` or as `spec-kit/input/product-prd.md`).

**The 8 artifacts it generates:**

| File | Contents |
|---------|-----------|
| `PRD.md` | Product Requirements Document: use cases, flows, product metrics |
| `technical-spec.md` | Technology stack, architecture, technical decisions, diagrams |
| `api-spec.yaml` | OpenAPI definition of all endpoints |
| `data-model.md` | Data model, entities, relationships, schemas |
| `epics.md` | High-level product epics |
| `stories.md` | User stories in the “As a [role], I want [action], so that [benefit]” format, with acceptance criteria |
| `sprint-plan.md` | Preliminary distribution of stories across sprints |
| `test-plan.md` | QA strategy by layer (unit, integration, end-to-end, performance) |

**How it works:**
1. Runs `node scripts/generate-spec-kit.js --mode local --brief spec-kit/input/brief.md`.
2. It can invoke `LOCAL_IDE_AI_COMMAND` if configured (optional).
3. Without an AI CLI, it uses deterministic fallbacks and the agent enriches the results.
4. Reads `planning/clarifications/spec_kit.json` if it exists.
5. Generates the 8 files in `spec-kit/input/`.
6. Marks the brief as approved in the workflow (implicit approval).

**What it generates:**
- 8 `.md` and `.yaml` files in `spec-kit/input/`.

**Next step:** `generate_sprints` (you can edit the artifacts first).

---

#### `generate_sprints`
**What it does:** Plans all sprints by distributing stories into executable sprints with concrete tasks.

**Preconditions:**
- All 8 Spec Kit artifacts exist in `spec-kit/input/`.

**How it works:**
1. Reads `epics.md`, `stories.md`, and `sprint-plan.md` from the Spec Kit.
2. Detects the number of sprints from `sprint-plan.md` (default: 3).
3. Distributes stories by sprint according to priority and dependencies.
4. For each sprint, generates:
   - `sprint-goal.md` → The specific sprint objective.
   - `stories.md` → Stories assigned to that sprint.
   - `tasks.md` → Technical tasks in this format:
     ```markdown
     | ID | Title | Story | Owner Role | Status | Points |
     |----|-------|-------|------------|--------|--------|
     | T-001 | Create User model | US-001 | backend | pending | 3 |
     | T-002 | Add /users API endpoint | US-001 | backend | pending | 5 |
     ```
   - `qa-plan.md` → Acceptance criteria and QA checklist.
5. Runs `node scripts/plan-all-sprints.js --mode local`.
6. Marks the spec as approved in the workflow (implicit approval).

**What it generates:**
- Folders such as `planning/sprints/sprint-01/`, `sprint-02/`, and so on.
- Four planning files in each folder.
- The total sprint count in `planning/workflow-state.json`.

**Next step:** `start_sprint --sprint 1`.

---

#### `start_sprint --sprint N`
**What it does:** **IMPLEMENTS** sprint N according to its plan. **The agent must write code in the same session**, not just run the script.

**Preconditions:**
- Sprints have been generated (`generate_sprints` has run).
- For N > 1, all tasks in sprint N-1 must be marked `done`.

**IMPORTANT — Agent contract:**

This command is **not just a script**. The agent is required to:
1. Run `node scripts/start-sprint.js --sprint N` (unless that sprint is already active).
2. In the **same response**, read the sprint plan:
   - `planning/sprints/sprint-0N/sprint-goal.md`
   - `stories.md`, `tasks.md`, and `qa-plan.md`
   - The Spec Kit when needed
3. **Implement real code** that fulfills the tasks.
4. Mark tasks `done` in `tasks.md` when they are complete.
5. If the backlog is large, deliver at least a **first vertical slice** (for example, routes + mock + basic player).

**How the script works:**
- For N > 1, it verifies that every task in sprint N-1 is `done`.
- It marks sprint N-1 complete (there is no `complete_sprint` command).
- It marks sprint N active in `planning/workflow-state.json`.

**Example:**
```
start_sprint --sprint 1
```

The agent might respond:
```markdown
**Agent(s):** `engineering-backend-architect` + `engineering-frontend-developer`

Sprint 1 activated: “Technical setup and data model”
```

## Implementation

Implemented:
- `/src/models/User.ts` → User model with validation.
- `/src/routes/users.ts` → CRUD endpoints at /api/users.
- `/tests/users.test.ts` → Unit tests for the model.

Completed tasks (marked `done` in tasks.md):
- T-001 ✓ Create User model
- T-002 ✓ Add /users API endpoint

Remaining: T-003, T-004, T-005
```

**Next step:**
- If tasks remain not `done` → `continue_sprint`.
- If all tasks are `done` → `start_sprint --sprint 2`.

---

#### `continue_sprint`
**What it does:** Resumes the active sprint when tasks remain (for example, after a new session or a pause).

**Preconditions:**
- A sprint is active in the workflow.
- At least one task in `tasks.md` is not `done`.

**How it differs from `start_sprint`:**

| Situation | Command |
|-----------|---------|
| Starting sprint N for the first time, or moving from completed N-1 to N | `start_sprint --sprint N` |
| Sprint N is already active and still has tasks not marked `done` | `continue_sprint` |

**How to use it:**
```
continue_sprint
```

The agent:
1. Runs `node scripts/continue-sprint.js` (which automatically uses the active sprint).
2. Reads the pending tasks in `tasks.md`.
3. **Continues implementing** in the same response.
4. Marks tasks `done` when they are complete.

**Next step:** When all tasks are `done` → `start_sprint --sprint N+1`.

---

#### `next_step`
**What it does:** Automatically detects and recommends (or executes) the next available workflow action.

**When to use it:**
- You are unsure which workflow stage you are in.
- You are returning to the project after several days.
- You want the agent to decide what to do next.

**How it works:**
1. Runs `node scripts/next-step.js`.
2. Reads `planning/workflow-state.json`.
3. Evaluates each command's preconditions.
4. Detects blockers (for example, missing refdocs or incomplete clarifications).
5. Recommends the most appropriate action.

**Example output:**
```json
{
  "nextAction": "continue_sprint",
  "sprint": 1,
  "blockers": [],
  "message": "Sprint 1 is active with 3 pending tasks. Run continue_sprint."
}
```

**Next step:** The agent runs the recommended action.

---

### 2. **Clarification Commands** (Optional)

These commands are **not required** in the workflow. The agent invokes them only when it finds ambiguities.

#### `clarify_brief`
**When to invoke it:**
- The refdocs are ambiguous, incomplete, or contradictory.
- The business objective or audience is unclear.
- You want to define UX preferences (theming, responsiveness, accessibility) before the brief.

**What it does:**
1. Reads all files in `refdocs/`.
2. Uses AI to generate 4–6 project-specific questions.
3. Adds fixed UX questions from `config/ux-clarification.json` (theming, devices, visual style, UI density, accessibility).
4. Presents the questions interactively.
5. Saves the answers to `planning/clarifications/brief.json`.

**Example questions:**
- What is the main priority: delivery speed, technical robustness, or user experience?
- What accessibility level is required (WCAG AA, AAA, or basic)?
- Must the system support light and dark themes?
- Must it be responsive (mobile + desktop), or desktop only?

**Next step:** `generate_brief` (it will use the answers as context).

---

#### `clarify_spec_kit`
**When to invoke it:**
- Sections of the brief are ambiguous or incomplete.
- The technology stack is unclear.
- There are questions about integrations, authentication, or the data model.

**What it does:**
1. Reads `spec-kit/input/brief.md`.
2. Generates 4–6 technical and product questions.
3. Saves the answers to `planning/clarifications/spec_kit.json`.

**Next step:** `generate_spec_kit`.

---

#### `clarify_sprints`
**When to invoke it:**
- Spec Kit stories are ambiguous or too broad.
- Feature prioritization is unclear.
- Team velocity or the number of sprints has not been defined.

**What it does:**
1. Reads `epics.md`, `stories.md`, `sprint-plan.md`, `PRD.md`, and `technical-spec.md`.
2. Generates 4–6 planning questions.
3. Saves the answers to `planning/clarifications/sprints.json`.

**Next step:** `generate_sprints`.

---

#### `clarify_sprint --sprint N`
**When to invoke it:**
- Acceptance criteria for sprint N tasks are ambiguous.
- Assigned roles are unclear.
- Technical dependencies are unresolved.

**What it does:**
1. Reads `planning/sprints/sprint-0N/sprint-goal.md`, `stories.md`, `tasks.md`, and `qa-plan.md`.
2. Generates 3–5 execution questions.
3. Saves the answers to `planning/clarifications/sprint-N.json`.

**Next step:** `start_sprint --sprint N`.

---

### 3. **Management Commands**

#### `update_spec_kit`
**What it does:** Downloads or updates the official GitHub Spec Kit templates.

**How it works:**
1. Verifies that `git` is available.
2. If `spec-kit/upstream/` does not exist, runs `git clone https://github.com/github/spec-kit.git`.
3. If it already exists, runs `git pull`.
4. Copies the templates to `spec-kit/templates/`.
5. Saves the commit SHA in `spec-kit/upstream.json`.

**When to use it:**
- The first time you use the framework.
- When you want to update the templates.

---

#### `update_agency_agents`
**What it does:** Downloads or updates agent roles from the Agency Agents repository.

**How it works:**
1. Verifies that `git` is available.
2. If `agency-agents/upstream/` does not exist, runs `git clone https://github.com/msitarzewski/agency-agents.git`.
3. If it already exists, runs `git pull`.
4. Copies roles into `agency-agents/agents/`.
5. Saves the commit SHA in `agency-agents/upstream.json`.

**Roles available by area:**

| Area | Primary roles |
|------|---------------|
| **Frontend** | `engineering-frontend-developer`, `design-ui-designer`, `design-ux-architect` |
| **Backend** | `engineering-backend-architect`, `engineering-database-optimizer`, `engineering-security-engineer` |
| **QA** | `testing-reality-checker`, `testing-api-tester`, `testing-accessibility-auditor`, `testing-performance-benchmarker` |
| **Deploy** | `engineering-devops-automator`, `engineering-site-reliability-engineer` |
| **PM** | `project-management-senior-project-manager`, `product-product-manager`, `project-management-project-shepherd` |

---

#### `reset_project`
**What it does:** Resets the project by removing generated artifacts (brief, spec, sprints, and state).

**WHAT IS REMOVED:**
- `spec-kit/input/brief.md` and generated Spec Kit artifacts.
- `planning/workflow-state.json` (returns to `created`).
- `planning/clarifications/` (all answers).
- `planning/sprints/` (all planned sprints).

**WHAT IS PRESERVED:**
- `refdocs/` (your input documents).
- `source-code/` (your source code).
- `spec-kit/templates/` (GitHub templates).
- `agency-agents/agents/` (agent roles).

**Precautions:**
- The agent must ask for explicit confirmation before running it.
- **This action cannot be undone.**

**Next step:** `init` (start over).

---

### 4. **Diagnostic Commands**

#### Check project status

From the terminal:
```bash
npm run status
# or
node scripts/status-report.js
```

Example output:
```text
Status: spec_generated
Brief: ✓ generated and approved
Spec Kit: ✓ generated (8 artifacts)
Sprints: ✗ not generated
Next action: generate_sprints
```

#### Interactive web dashboard

```bash
npm run status:web
```

Opens a dashboard at `http://localhost:4173` with:
- Visual workflow status.
- Generated artifacts.
- Active and completed sprints.
- Recommended next action.

---

## 🎭 Agent Role System (Agency Agents)

### What is an agent role?

Each role is a **specialized persona** the IDE agent adopts for specific tasks. Roles are defined under `agency-agents/agents/<category>/` as Markdown files containing:
- The role's **specialty**.
- Its main **responsibilities**.
- The **tools** it uses.
- Recommended **work patterns**.
- The **quality criteria** it must meet.

### Agent declaration (required)

**Rule:** At the start of every VISION activity, the agent must declare which role(s) it is applying.

**Format:**
```markdown
**Agent(s):** `role-slug` + `role-slug-2` (optional reason)
```

**Example:**
```markdown
**Agent(s):** `engineering-backend-architect` + `engineering-database-optimizer`

I will implement the data model and API endpoints...
```

### Suggested roles by command

| Command | Suggested roles |
|---------|-----------------|
| `init` | `project-management-studio-operations-manager`, `engineering-devops-automator` |
| `clarify_brief` | `product-product-manager`, `design-ux-researcher` |
| `generate_brief` | `product-product-manager`, `engineering-technical-writer` |
| `generate_spec_kit` | `engineering-software-architect`, `engineering-backend-architect`, `engineering-frontend-developer` |
| `generate_sprints` | `product-sprint-prioritizer`, `project-management-project-shepherd` |
| `start_sprint` | `project-management-senior-project-manager` + technical roles according to tasks (frontend/backend/QA/deploy) |
| `continue_sprint` | Same as `start_sprint` |

### Roles by technical area

When implementing sprint code, the agent uses specialized roles:

**Frontend:**
- `engineering-frontend-developer` → React/Vue components, UI, state.
- `design-ui-designer` → Visual design, theming, tokens.
- `design-ux-architect` → User flows, navigation, accessibility.

**Backend:**
- `engineering-backend-architect` → API, domain, architecture.
- `engineering-database-optimizer` → Queries, indexes, migrations.
- `engineering-security-engineer` → Authentication, authorization, encryption.

**QA:**
- `testing-reality-checker` → Acceptance-criteria validation.
- `testing-api-tester` → Endpoint and contract tests.
- `testing-accessibility-auditor` → WCAG, screen readers, keyboard navigation.

**Deploy:**
- `engineering-devops-automator` → CI/CD, Docker, deployment scripts.
- `engineering-site-reliability-engineer` → Monitoring, logging, alerts.

---

## 📊 Workflow State (`workflow-state.json`)

`planning/workflow-state.json` is the **brain of the framework**. It tracks:

```json
{
  "version": 1,
  "setup": {
    "reverseEngineering": true,
    "referencesDir": "refdocs",
    "sourceDir": "source-code",
    "initializedAt": "2026-04-06T15:51:06.762Z"
  },
  "status": "spec_generated",
  "brief": {
    "generated": true,
    "approved": true,
    "path": "spec-kit/input/brief.md",
    "generatedAt": "2026-04-06T16:14:59.000Z",
    "approvedAt": "2026-04-06T16:17:53.459Z"
  },
  "spec": {
    "generated": true,
    "approved": false,
    "mode": "local",
    "generatedAt": "2026-04-06T16:17:53.459Z",
    "artifactFiles": [
      "PRD.md",
      "technical-spec.md",
      "api-spec.yaml",
      "data-model.md",
      "epics.md",
      "stories.md",
      "sprint-plan.md",
      "test-plan.md"
    ]
  },
  "sprints": {
    "generated": false,
    "approved": false,
    "total": 0,
    "active": null,
    "completed": []
  },
  "lastAction": {
    "command": "generate_spec_kit",
    "at": "2026-04-06T16:17:53.459Z"
  }
}
```

### Workflow states

| State | Description |
|--------|-------------|
| `created` | Project initialized, but no brief exists |
| `brief_generated` | Brief generated but not approved |
| `brief_approved` | Brief approved and ready for the spec |
| `spec_generated` | Spec Kit generated |
| `spec_approved` | Spec approved and ready for sprint planning |
| `sprints_generated` | Sprint plan generated |
| `sprints_approved` | Plan approved and ready for execution |
| `sprint_active` | A sprint is in progress |

### ⚠️ **Do not edit this file manually**

Scripts update it automatically. Manual changes can break the workflow.

---

## ⚙️ Configuration (`.env`)

The `.env` file is **optional**. VISION works without it, but you can customize the framework:

```bash
# Project name
PROJECT_NAME=My Awesome Project
PROJECT_SLUG=my-project

# Local AI (OPTIONAL — the IDE agent is the default local AI)
LOCAL_AI_PROVIDER=ide_command
LOCAL_IDE_AI_COMMAND=
LOCAL_IDE_AI_TIMEOUT_MS=180000

# Web dashboard port
STATUS_WEB_PORT=4173

# Matrix connection (OPTIONAL — only if you use the central service)
MATRIX_BASE_URL=
MATRIX_API_KEY=
```

### What is `LOCAL_IDE_AI_COMMAND`?

It is **optional**. If configured, it points to an external CLI that:
- Reads a JSON input (`{{INPUT_FILE}}`).
- Processes it with AI (for example, using OpenAI, Anthropic, or a local LLM).
- Writes a JSON output (`{{OUTPUT_FILE}}`).

**However:** The framework's “local AI” is the **IDE agent**. Commands expect the agent to read, analyze, and write project artifacts. If `LOCAL_IDE_AI_COMMAND` is not set, scripts use deterministic fallbacks and the agent enriches the results manually.

---

## 🎓 Tutorial: Your First Project

### Step 1: Prepare your documents

1. Place your project documents in `refdocs/`:
   - Requirements PDFs.
   - Markdown specifications.
   - JSON sample data.
   - Mockup images.
   - Meeting transcripts.
2. For reverse engineering, place the existing code in `source-code/`.

### Step 2: Initialize

In your IDE agent window, enter:
```
init
```

Answer the questions:
- **Is this reverse engineering?** Choose `yes` if you have existing code; choose `no` if you are starting from scratch.
- **Update Spec Kit?** Choose `yes` the first time.
- **Update Agency Agents?** Choose `yes` the first time.

### Step 3: Generate the brief

If your documents are clear:
```
generate_brief
```

If they are ambiguous:
```
clarify_brief
```
Answer the questions, then run:
```
generate_brief
```

**Result:** `spec-kit/input/brief.md`, your executive brief.

### Step 4: Generate the Spec Kit

```
generate_spec_kit
```

The agent reads the brief and generates the 8 technical artifacts.

**Result:** 8 files in `spec-kit/input/` (PRD, technical spec, API spec, data model, epics, stories, sprint plan, and test plan).

### Step 5: Plan sprints

```
generate_sprints
```

The agent distributes stories into executable sprints.

**Result:** `planning/sprints/sprint-01/`, `sprint-02/`, and other sprint folders with detailed plans.

### Step 6: Develop the first sprint

```
start_sprint --sprint 1
```

**The agent writes code in the same response:**
- Creates files according to `tasks.md`.
- Implements the features.
- Marks tasks `done` when complete.
- Shows you the generated code.

### Step 7: Continue if needed

If you paused work:
```
continue_sprint
```

The agent resumes pending tasks.

### Step 8: Move to the next sprint

When all tasks in sprint 1 are `done`:
```
start_sprint --sprint 2
```

The script automatically closes sprint 1 and starts sprint 2.

---

## 🔍 Frequently Asked Questions

### Can I edit generated artifacts?

**Yes.** All files in `spec-kit/input/` and `planning/sprints/` are editable. The framework uses them as input, not as untouchable files.

### What should I do if a command fails?

1. Read the error message; it identifies the missing precondition.
2. Run `next_step` to see the diagnosis.
3. Resolve the blocker (for example, add files to `refdocs/` or complete pending tasks).
4. Run the command again.

### Can I skip clarification commands?

**Yes.** `clarify_*` commands are optional. If your documents are clear, go directly to `generate_brief`, `generate_spec_kit`, and so on.

### Can I change the number of sprints?

Yes. Edit `spec-kit/input/sprint-plan.md` before running `generate_sprints`. The script detects the number of sprints from the document.

### How do I mark a task as complete?

In `planning/sprints/sprint-0N/tasks.md`, change its status:
```markdown
| T-001 | Create User model | US-001 | backend | done | 3 |
```

### Can I work on multiple sprints in parallel?

No. The workflow allows only one active sprint at a time. Complete all tasks in sprint N before moving to N+1.

### What if I need more sprints?

1. Add a `sprint-0X/` folder under `planning/sprints/`.
2. Create its four files: `sprint-goal.md`, `stories.md`, `tasks.md`, and `qa-plan.md`.
3. Update `sprints.total` in `planning/workflow-state.json`.

---

## 📦 IDE Compatibility

VISION works with multiple IDEs. Each has its own entrypoint:

| IDE | Entrypoint | How to activate |
|-----|------------|-----------------|
| **Cursor** | `.cursor/rules/vision-index.mdc` | Always-on (loaded automatically) |
| **Claude Code** | `CLAUDE.md` + `.claude/rules/vision-workflow.md` + slash commands in `.claude/commands/` | Always-on + `/generate_brief`, `/start_sprint`, etc. |
| **GitHub Copilot** | `.github/copilot-instructions.md` | Always-on |
| **Antigravity (Gemini)** | Global skills in `~/.gemini/antigravity/skills/` | `@vision-generate-brief`, `@vision-start-sprint`, etc. |
| **Continue.dev** | Point directly to `commands/<name>.md` | Manual setup |

### Generate Antigravity skills

```bash
npm run generate_skills:antigravity
```

Installs one SKILL.md per command under `~/.gemini/antigravity/skills/vision-<command>/`.

Activate with:
```
@vision-generate-brief
@vision-start-sprint
@vision-agent-declaration
```

---

## 🚀 Commands from the Terminal (Optional)

Every command also has an `npm run` equivalent for direct terminal use:

```bash
npm run init
npm run clarify_brief
npm run generate_brief
npm run generate_spec_kit
npm run generate_sprints
npm run start_sprint -- --sprint 1
npm run continue_sprint
npm run next_step
npm run status
npm run status:web
npm run reset_project
```

Or use the wrappers in `bin/`:
```bash
bin/generate_brief
bin/start_sprint --sprint 1
```

---

## 🎯 Best Practices

### 1. Provide good context in refdocs

The more context you provide in `refdocs/`, the better the brief and spec will be. Include:
- Functional and non-functional requirements.
- Mockups or wireframes.
- Architecture diagrams.
- Business rules.
- Use cases.
- Acceptance criteria.

### 2. Review artifacts before continuing

After every generation step, **review the artifacts**:
- After `generate_brief` → Read and edit `spec-kit/input/brief.md`.
- After `generate_spec_kit` → Verify all 8 artifacts.
- After `generate_sprints` → Review the sprint plan.

### 3. Mark tasks `done` only when they are verifiable

Do not mark a task `done` if:
- The code does not compile.
- Tests do not pass.
- It does not meet the acceptance criteria.

### 4. Use clarification when helpful

If something is unclear, run `clarify_*` **before** generating artifacts. Clarifying first is more efficient than regenerating later.

### 5. Use agent roles

When the agent declares its roles at the beginning of a response, **expect that specialization**. For example, a `testing-reality-checker` should validate the work critically.

---

## 📚 Additional Resources

### Reference documentation

- [AGENTS.md](AGENTS.md) → Quick reference for all commands.
- [commands/](commands/) → Detailed instructions for each command.
- [planning/](planning/) → Planning structure and workflow state.
- [spec-kit/](spec-kit/) → Spec Kit structure and templates.

### External repositories

- **Spec Kit:** https://github.com/github/spec-kit
- **Agency Agents:** https://github.com/msitarzewski/agency-agents

---

## 🎉 Conclusion

VISION Framework is a **complete development methodology** that turns project documents into implementable software through a structured, AI-assisted workflow.

**What makes VISION special:**
1. **Sequential and validated:** Each step has clear preconditions.
2. **Agent-driven:** The IDE agent does the heavy lifting.
3. **Template-based:** Uses professional standards (GitHub Spec Kit).
4. **Role-based:** Specialization by technical area (Agency Agents).
5. **Local-first:** Everything stays on your machine, with no mandatory dependencies.
6. **Traceable:** Workflow state is transparent and verifiable.

**Your next step:**

Enter this in your IDE agent window:
```
init
```

Start your first VISION project!

---

*Guide for VISION Framework v1.0.0*
*Last updated: 2026-04-06*
