> 🤖 **If you are an AI agent:** Read **`AGENT.md`** first — that is your operating manual.

---

# QE-Copilot — AI-Native Test Automation Framework

> **Declarative YAML test cases. Zero automation scripts. Driven entirely by AI.**

QE-Copilot is an open-source test automation framework where an AI agent (GitHub Copilot, Claude, or OpenAI Codex) reads YAML-defined test cases and executes them directly through [Playwright MCP](https://github.com/microsoft/playwright-mcp) — no test scripts are generated, no Selenium wrappers, no boilerplate.

---

## How It Works

1. You define test cases in YAML — steps, expected outcomes, test data
2. The AI agent reads `AGENT.md` (its operating manual) at the start of every run
3. The agent executes each step via Playwright MCP browser tools (`browser_navigate`, `browser_click`, etc.)
4. Screenshots, reports, and a self-updating knowledge graph are written automatically

**No code. No scripts. Just describe what to test.**

---

## Prerequisites — Configure Playwright MCP

**Required before running any tests.** The framework drives the browser through Playwright MCP tools. Configure it for your AI agent:

### GitHub Copilot CLI

Create `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "playwright": {
      "type": "local",
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--browser", "msedge"],
      "env": {},
      "tools": ["*"]
    }
  }
}
```

> **Windows:** Use `msedge` (pre-installed). **macOS/Linux:** Use `"chrome"` instead.
>
> Browser behaviour (maximize, slow_mo etc.) is configured in `configs/ai-test-config.yml` — not here.

Or add interactively:
```
/mcp add
# Server Name: playwright
# Server Type: Local (STDIO)
# Command: npx @playwright/mcp@latest --browser msedge
```

Verify:
```
/mcp show playwright
```

### Claude (Claude Code / Claude Desktop)

Add to `.claude/settings.local.json` in your project root:
```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--browser", "msedge"]
    }
  }
}
```

### Copilot Cloud Agent

No setup needed — Playwright MCP is built in by default.

### OpenAI Codex

Configure per your Codex deployment environment.

---

## Quick Start

```bash
# 1. Copy this scaffold to your project
cp -r qe-copilot my-app-tests
cd my-app-tests

# 2. Set up environment variables
cp .env.example .env
# Edit .env: set BASE_URL and credentials

# 3. Fill in application knowledge
# Edit knowledge/modules.json  — UI elements, button labels, field types
# Edit knowledge/entities.json — data models, ID formats
# Edit knowledge/workflows.json — business workflows

# 4. Write your first test case
# Copy templates/testcase.yml → testcases/<feature>/TC-001.yml

# 5. Write the actions it needs
# Copy templates/action.yml → actions/<category>/<name>.yml
# Register in actions/action-catalog.yml

# 6. Start your AI agent from this directory
gh copilot      # or claude / codex
```

---

## Running Tests with Copilot CLI

Every new Copilot CLI session requires two setup commands:

```
/allow-all
```
Grants all tool, file, and URL permissions — without this, Copilot prompts for approval on every browser action.

```
/mcp show playwright
```
Verifies the Playwright MCP server is active. You should see `browser_navigate`, `browser_click` etc.

**Recommended flow every session:**
```
cd /path/to/my-app-tests
gh copilot

/allow-all
/mcp show playwright

Run the smoke suite
```

> `/allow-all` resets on exit — run it at the start of every new session.

---

## Framework Structure

```
qe-copilot/
├── AGENT.md                   ← AI agent operating manual (read this first)
├── README.md
├── .env                       ← Runtime secrets (gitignored)
├── .env.example               ← Template — copy to .env
│
├── configs/
│   ├── ai-test-config.yml     ← Global settings (browser, suite, environment)
│   ├── qa.yml                 ← QA environment
│   ├── uat.yml                ← UAT environment
│   └── prod.yml               ← Production environment
│
├── testcases/                 ← Test case definitions (YAML)
│   └── <feature>/TC-NNN.yml
│
├── actions/                   ← Reusable action libraries (YAML)
│   ├── action-catalog.yml     ← Master index of all actions
│   └── common/
│       ├── auth.yml           ← Login / logout
│       └── navigation.yml     ← Navigation actions
│
├── testdata/
│   ├── users.yml              ← User personas and credentials
│   ├── entities.yml           ← Domain entities (rename to match your domain)
│   └── environments.yml       ← Environment URLs and flags
│
├── knowledge/                 ← Application knowledge graph (auto-updated each run)
│   ├── modules.json           ← UI elements, button labels, field types per page/module
│   ├── entities.json          ← Data models, ID formats, field constraints
│   ├── workflows.json         ← Business workflows and status transitions
│   ├── api_catalog.json       ← REST API endpoints
│   └── learning-log.json      ← Cross-run learning history
│
├── suites/
│   └── smoke.yml              ← Test suite definitions
│
├── templates/                 ← Blank templates for creating new assets
│   ├── testcase.yml
│   ├── action.yml
│   ├── defect.md
│   ├── execution.json
│   ├── report.json
│   ├── run-context.yml
│   └── knowledge-update.json
│
├── pipelines/
│   └── execution-pipeline.yml
│
├── runs/                      ← Auto-generated run evidence (gitignored)
└── reports/                   ← Quick-access reports (gitignored)
    ├── latest/
    └── history/
```

---

## Key Concepts

### AGENT.md — The AI Agent's Operating Manual

`AGENT.md` is the most important file. Every agent reads it at the start of every run. It defines the boot sequence, execution phases, YAML schemas, variable resolution, MCP tool usage, and knowledge update protocol.

### Knowledge Graph

The `knowledge/` folder is your application's persistent memory. The agent reads it before planning and writes to it after every run — capturing discovered UI elements, field types, business rules, and retry patterns. It grows automatically with each run.

### Reusable Actions

Actions are composable building blocks. A test case step like `Login as Admin` resolves to an action defined in `actions/common/auth.yml`. Actions are parameterised and reused across test cases.

---

## Writing Test Cases

Copy `templates/testcase.yml` to `testcases/<feature>/TC-NNN.yml`:

```yaml
test_case:
  id: TC-001
  title: "Create a new record"
  feature: "Records"
  priority: high
  persona: admin

  test_data:
    user: users.admin1

  steps:
    - Login as Admin
    - Navigate to Records
    - Create Record
    - Save

  step_params:
    Create Record:
      name: "${testdata.entities.record1.name}"

  expected:
    - "Record is created successfully"
    - "Record appears in the list"
```

---

## Writing Actions

Copy `templates/action.yml` to `actions/<category>/<name>.yml`:

```yaml
actions:
  - action_id: create_record
    name: "Create Record"
    description: "Opens the new record form and fills required fields"
    parameters:
      - name: name
        required: true
    steps:
      - description: "Click the New button"
        tool: browser_click
        params:
          element: "New Record button"
      - description: "Type record name"
        tool: browser_type
        params:
          element: "Name input"
          text: "${params.name}"
      - description: "Screenshot of filled form"
        tool: browser_take_screenshot
```

---

## Test Results

**Quick access — `reports/latest/`** (and `reports/history/<run-id>/`):

| File | Contents |
|------|----------|
| `report.html` | Human-readable pass/fail summary |
| `report.json` | Machine-readable summary |

**Full evidence — `runs/<run-id>/`** (navigate here using `run_id` from the report):

| Path | Contents |
|------|----------|
| `artifacts/screenshots/` | Step-by-step screenshots |
| `defects/BUG-NNN.md` | Auto-generated defect report per failure |
| `ai-reasoning/` | Agent decision logs |
| `knowledge-updates.json` | Discoveries made during this run |

---

## Supported AI Agents

| Agent | Playwright MCP | Setup |
|-------|---------------|-------|
| GitHub Copilot CLI | ✅ Manual config | See Prerequisites above |
| GitHub Copilot Cloud Agent | ✅ Built-in | No setup needed |
| Claude Code / Claude Desktop | ✅ Via settings.local.json | See Prerequisites above |
| OpenAI Codex | ✅ Via deployment config | Configure per environment |

---

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.
