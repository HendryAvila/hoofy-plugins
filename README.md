<p align="center">
  <img src="https://raw.githubusercontent.com/HendryAvila/Hoofy/main/assets/logo.png" alt="Hoofy" width="200" />
</p>

<h1 align="center">Hoofy Plugins</h1>

<p align="center">
  <strong>Official Claude Code plugin marketplace for Hoofy.</strong><br>
  One command to install. Zero config. Specs before code.
</p>

---

## Install

Inside Claude Code, run:

```
/plugin marketplace add HendryAvila/hoofy-plugins
/plugin install hoofy@hoofy-plugins
```

That's it. You now have:

- 🐴 **Hoofy agent** — sarcastic horse-architect that teaches through humor
- 🛠️ **26 MCP tools** — persistent memory, change pipeline, project pipeline
- ⚡ **3 skills** — `/hoofy:init`, `/hoofy:change`, `/hoofy:review`
- 🔄 **Lifecycle hooks** — auto-loads memory on session start, guides pipeline usage

## What's included

| Component | What it does |
|---|---|
| **Agent** | Hoofy personality — teaches concepts over code, enforces spec-driven development |
| **MCP Server** | 14 memory tools + 5 change pipeline tools + 8 project pipeline tools |
| **Skills** | `/hoofy:init` (start SDD project), `/hoofy:change` (adaptive change pipeline), `/hoofy:review` (check pipeline status) |
| **Hooks** | `SessionStart` loads memory context, `PostToolUse` guides pipeline progression |

## Prerequisites

Hoofy MCP server must be installed on your system:

```bash
curl -sSL https://raw.githubusercontent.com/HendryAvila/Hoofy/main/install.sh | bash
```

## Update

To get the latest version:

```
/plugin marketplace update
/plugin install hoofy@hoofy-plugins
```

## Links

- [Hoofy repo](https://github.com/HendryAvila/Hoofy) — MCP server source code
- [Plugin docs](https://github.com/HendryAvila/Hoofy/tree/main/plugins/claude-code) — detailed plugin documentation

---

<p align="center">
  <strong>Stop prompting. Start specifying.</strong>
</p>
