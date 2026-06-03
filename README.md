<div align="center">

<img src="opencode-profile-pic/frosty_icon.png" alt="Frostylicious" width="120" />

# Frostylicious — AI Research & Automation Assistant

**A research-first AI agent for [OpenCode Desktop](https://opencode.ai) that controls your Chrome browser to research, automate, and get things done on the internet.**

[![OpenCode](https://img.shields.io/badge/OpenCode-Desktop-FF6B9D?style=for-the-badge)](https://opencode.ai)
[![Chrome DevTools MCP](https://img.shields.io/badge/Chrome_DevTools-MCP-4285F4?style=for-the-badge&logo=googlechrome&logoColor=white)](https://www.npmjs.com/package/chrome-devtools-mcp)

---

**Research anything. Automate everything. Free AI models.**

[Getting Started](#getting-started) · [What It Can Do](#what-it-can-do) · [How It Works](#how-it-works)

</div>

---

## What Is Frostylicious?

Frostylicious is a general-purpose AI assistant that runs inside [OpenCode Desktop](https://opencode.ai) and controls your Chrome browser via Chrome DevTools. Unlike single-purpose agents, Frostylicious is a **generalist** — it can handle anything from deep web research to browser automation to technical debugging.

**Core strengths:**

- **Research-first mindset** — doesn't guess, verifies everything via the browser
- **Full browser control** — navigates, clicks, fills forms, extracts data, takes screenshots
- **AutoConnect** — uses your actual Chrome with all logged-in sessions (Gmail, Notion, Shopify, etc.)
- **Self-improving** — auto-creates reusable skills for novel workflows
- **Knowledge base** — grows smarter as you add context files
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
┌──────────────────────────────────────────────────────┐
│                   OpenCode Desktop                    │
│  ┌──────────────┐  ┌────────────┐  ┌──────────────┐  │
│  │ Frostylicious │  │ AGENTS.md  │  │  Knowledge   │  │
│  │    Agent      │──│  (Rules)   │──│  (Growing)   │  │
│  └──────┬───────┘  └────────────┘  └──────────────┘  │
│         │                                             │
│  ┌──────▼────────────────┐  ┌──────────────────────┐  │
│  │  Chrome DevTools MCP  │  │  Skills (Auto-grow)  │  │
│  │  (AutoConnect)        │  │  User Profile        │  │
│  └──────┬────────────────┘  └──────────────────────┘  │
└─────────┼─────────────────────────────────────────────┘
          │
   ┌──────▼──────────────┐
   │  Your Chrome Browser │
   │  (All logged-in      │
   │   sessions available) │
   └──────────────────────┘
```

### Research Flow

1. You ask a question or give a task
2. Frostylicious checks knowledge files for existing context
3. If unsure → opens Chrome, searches Google or relevant sites
4. Reads multiple sources, cross-references
5. Synthesizes findings with citations
6. Creates a skill if the workflow was novel

### User Profile

On first interaction, Frostylicious asks your name and saves it to `user/user.txt`. On subsequent sessions, it reads the file and greets you by name. It can also save your preferences and habits as you share them.

---

## Project Structure

```
frostylicious-opencode-agent/
├── .opencode/
│   ├── AGENTS.md                    ← Agent personality, rules, and workflows
│   └── skills/                      ← Auto-created skills (grows over time)
├── knowledge/                       ← Your knowledge base (add .md files here)
├── user/
│   └── user.txt                     ← User profile (auto-created on first use)
├── tmp/                             ← Temporary working files
├── logs/                            ← Session logs
├── opencode.json                    ← Agent configuration
├── opencode.jsonc                   ← MCP and provider config
├── .gitignore
└── README.md
```

---

## Adding Knowledge

Drop any `.md` file into `knowledge/` or create subfolders to organize by topic:

```
knowledge/
├── company-info.md
├── product-docs.md
├── competitor-research/
│   ├── competitor-a.md
│   └── competitor-b.md
└── processes/
    ├── onboarding-workflow.md
    └── support-playbook.md
```

All files are automatically loaded into Frostylicious's context on every session.

---

## Customization

### Changing the Personality

Edit `.opencode/AGENTS.md` — change the name, tone, role, and focus areas.

### Adding Domain Expertise

Drop `.md` files into `knowledge/` with domain-specific information. Frostylicious automatically uses them.

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
