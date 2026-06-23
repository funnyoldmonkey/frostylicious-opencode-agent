<div align="center">

<img src="opencode-profile-pic/frosty_icon.png" alt="Frostylicious" width="120" />

# Frostylicious — AI Research & Automation Assistant

**A research-first AI agent for [OpenCode Desktop](https://opencode.ai) with webfetch-first research, Chrome DevTools for interactive tasks, a growing knowledge base, and auto-created skills.**

[![OpenCode](https://img.shields.io/badge/OpenCode-Desktop-FF6B9D?style=for-the-badge)](https://opencode.ai)
[![Chrome DevTools MCP](https://img.shields.io/badge/Chrome_DevTools-MCP-4285F4?style=for-the-badge&logo=googlechrome&logoColor=white)](https://www.npmjs.com/package/chrome-devtools-mcp)

---

**Research anything. Automate everything. Free AI models.**

[Getting Started](#getting-started) · [What It Can Do](#what-it-can-do) · [How It Works](#how-it-works)

</div>

---

## What Is Frostylicious?

Frostylicious is a general-purpose AI assistant that runs inside [OpenCode Desktop](https://opencode.ai). It uses `webfetch` for fast, lightweight research and escalates to Chrome DevTools for interactive browser tasks. It's a **generalist** — deep web research, browser automation, data extraction, content creation, technical debugging.

**Core strengths:**

- **Webfetch-first research** — fast lookups without opening browser tabs. Escalates to Chrome DevTools when pages need JS rendering or interaction.
- **Full browser control** — navigates, clicks, fills forms, extracts data, takes screenshots via Chrome DevTools with AutoConnect
- **@explore subagent** — searches the knowledge base before every task, returning only relevant context to save tokens
- **Mandatory skill check** — always checks for existing skills before executing multi-step tasks
- **Self-improving** — auto-creates reusable skills for novel workflows, logs verified workflows
- **Session logging** — full audit trail of every non-trivial session
- **User profile** — remembers who you are across sessions

---

## What It Can Do

| Category | Examples |
|---|---|
| **Deep Research** | Multi-source research, competitor analysis, fact-checking, documentation lookup |
| **Web Automation** | Fill forms, submit data, navigate multi-step workflows, interact with web apps |
| **Data Extraction** | Scrape tables, prices, product info, structured data from any website |
| **Content & Social** | Draft posts, browse feeds, research trends, manage content |
| **Technical Debugging** | Inspect DOM, check console errors, test CSS/JS fixes, performance audits |
| **Admin Tasks** | Navigate dashboards, manage settings, export data, process workflows |

---

## Getting Started

### Prerequisites

| Requirement | How to Get It |
|---|---|
| **OpenCode Desktop** | Download from [opencode.ai](https://opencode.ai) |
| **Node.js** (v18+) | [nodejs.org](https://nodejs.org) — needed for Chrome DevTools MCP |
| **Python 3.10+** | [python.org](https://python.org) — needed for the API key rotator and verified-workflows append |
| **Git** | [git-scm.com](https://git-scm.com) |

### Step 1: Clone the Repository

```bash
git clone https://github.com/funnyoldmonkey/frostylicious-opencode-agent.git
cd frostylicious-opencode-agent
```

### Step 2: Enable Chrome AutoConnect

1. Open your Chrome browser
2. Navigate to `chrome://inspect/#remote-debugging`
3. Follow the dialog to **enable remote debugging**
4. You should see: `Server running at 127.0.0.1:9222`

> **Note:** Re-enable this each time you restart Chrome.

### Step 3: Set Up API Key Rotator

The included API key rotator round-robins your Google API keys to avoid rate limits.

```bash
cp api-key-rotator/keys.txt.example api-key-rotator/keys.txt
```

Edit `api-key-rotator/keys.txt` and add your Google API keys (one per line). Then start it:

```bash
python api-key-rotator/rotator.py
```

The `opencode.jsonc` is already configured to route through `http://127.0.0.1:5555`.

If not using the rotator, remove the `provider` section from `opencode.jsonc` and use Open Code's built-in provider setup.

### Step 4: Open in OpenCode Desktop

1. Open OpenCode Desktop
2. Open the `frostylicious-opencode-agent` folder as your project
3. Frostylicious should appear as the default agent (bottom-left)
4. Select your preferred model
5. Start chatting!

On first message, Frostylicious will ask your name and save it for future sessions.

---

## How It Works

### Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    OpenCode Desktop                       │
│  ┌──────────────┐  ┌────────────┐  ┌──────────────────┐  │
│  │ Frostylicious │  │ AGENTS.md  │  │  @explore        │  │
│  │    Agent      │──│  (Rules)   │  │  (Knowledge      │  │
│  └──────┬───────┘  └────────────┘  │   Search)        │  │
│         │                          └──────────────────┘  │
│  ┌──────▼────────────────┐  ┌──────────────────────────┐  │
│  │  Chrome DevTools MCP  │  │  Skills (Auto-grow)      │  │
│  │  (AutoConnect)        │  │  Verified Workflows      │  │
│  └──────┬────────────────┘  └──────────────────────────┘  │
└─────────┼────────────────────────────────────────────────┘
          │
   ┌──────▼──────────────┐     ┌───────────────────────┐
   │  Your Chrome Browser │     │  webfetch             │
   │  (Interactive tasks)  │     │  (Default research)   │
   └──────────────────────┘     └───────────────────────┘
```

### Task Flow

When you give Frostylicious a task, it follows this sequence:

1. **Understand** — parse the request, ask clarifying questions if needed
2. **Research** — use `webfetch` for quick lookups, escalate to Chrome DevTools for JS-rendered or interactive pages
3. **Consult @explore** (MANDATORY) — searches knowledge files for verified workflows and relevant context
4. **Check skills** (MANDATORY) — lists and reads matching skills before executing
5. **Execute** — perform the task using the appropriate tools
6. **Verify** — double-check work, screenshot web tasks, spot-check data
7. **Deliver** — present results with sources and next steps
8. **Log session** — write audit trail to `logs/`
9. **Log workflow** — if user confirms, append to `verified-workflows.md`
10. **Create skill** — save novel patterns for future use

### Knowledge System

```
knowledge/
└── verified-workflows.md        ← Auto-maintained log of confirmed workflows
└── (your files here)            ← Drop .md files to expand knowledge
```

Knowledge files are searched **on demand** by the `@explore` subagent. Frostylicious calls @explore before executing tasks — it searches verified workflows and knowledge files, returning only relevant context. Add new `.md` files anytime — @explore finds them on the next call.

### User Profile

On first interaction, Frostylicious asks your name and saves it to `user/user.txt`. On subsequent sessions, it reads the file and greets you by name. It can also save your preferences and habits as you share them.

---

## Project Structure

```
frostylicious-opencode-agent/
├── .opencode/
│   ├── AGENTS.md                    ← Agent personality, rules, and workflows
│   └── skills/                      ← Auto-created skills (grows over time)
├── knowledge/
│   └── verified-workflows.md        ← Auto-maintained workflow log
├── user/
│   └── user.txt                     ← User profile (auto-created on first use)
├── tmp/                             ← Temporary working files
├── logs/                            ← Session logs (auto-created)
├── api-key-rotator/
│   ├── rotator.py                   ← Key rotation proxy
│   └── keys.txt.example             ← Template for API keys
├── opencode.json                    ← Agent and subagent configuration
├── opencode.jsonc                   ← MCP servers and provider config
├── .gitignore
└── README.md
```

---

## Adding Knowledge

Drop any `.md` file into `knowledge/` or create subfolders to organize by topic:

```
knowledge/
├── verified-workflows.md
├── company-info.md
├── product-docs.md
├── competitor-research/
│   ├── competitor-a.md
│   └── competitor-b.md
└── processes/
    ├── onboarding-workflow.md
    └── support-playbook.md
```

@explore searches these on demand — no need to pre-load everything into context.

---

## Customization

### Changing the Personality

Edit `.opencode/AGENTS.md` — change the name, tone, role, and focus areas.

### Adding Domain Expertise

Drop `.md` files into `knowledge/` with domain-specific information. @explore finds and uses them automatically.

### Creating Manual Skills

Create `.opencode/skills/<name>/SKILL.md` with a specific workflow. Frostylicious discovers and uses them automatically.

---

## Troubleshooting

### Frostylicious doesn't respond

Delete `.opencode/tools/` if it exists — custom tools crash OpenCode Desktop on Windows.

### Chrome not connecting

1. Make sure Chrome is running
2. Go to `chrome://inspect/#remote-debugging` and enable it
3. Restart OpenCode Desktop

### MCP servers don't appear in UI

This is a known OpenCode bug. The tools still load and work correctly from the project config — it's just the UI that doesn't show them.

---

## License

MIT — see [LICENSE](LICENSE).

---

## Credits

Built on the [Frosty](https://github.com/funnyoldmonkey/frosty-opencode-agent) architecture.
