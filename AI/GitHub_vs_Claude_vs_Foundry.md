Here's the comparison with Copilot as the baseline, grouped by what you're configuring. Claude Code details come from its docs and community references, and a few points are marked where I'm less certain.

## 1. Instructions and context

| | Copilot (VS Code) | Claude Code | Foundry |
|---|---|---|---|
| **Always-on** | `.github/copilot-instructions.md` for repo-wide guidance, plus `AGENTS.md` for third-party agents. | `CLAUDE.md` files merge from several levels: `~/.claude/CLAUDE.md` (global), `./CLAUDE.md` or `./.claude/CLAUDE.md` (project), and a gitignored `CLAUDE.local.md` for personal notes. Everything loaded counts against context every session, so keep it short. | One `instructions` string on the agent definition. An agent is a model plus instructions plus tools, created once so you don't resend a system prompt on every request. There is no file hierarchy and no merging. |
| **Scoped by path** | `.github/instructions/*.instructions.md` with an `applyTo` glob. | `.claude/rules/*.md`, modular rule files, which can be scoped to paths in their frontmatter (I'm fairly sure of this, so verify it in the docs). `CLAUDE.md` files in subdirectories also apply when Claude works there. | Not applicable. The agent isn't working inside a repo, so there's nothing to scope to. |
| **Composition** | Instructions files reference other files via markdown links. | `@path/to/file` imports, for example `@docs/architecture.md`, pull other files into `CLAUDE.md`. | You write the instruction text yourself. Reuse comes from skills or from a shared toolbox. |
| **Cross-tool standard** | `AGENTS.md` | Reads `CLAUDE.md`, and also reads `AGENTS.md` if present. | None. |

## 2. Reusable prompts, skills, and custom agents

| | Copilot (VS Code) | Claude Code | Foundry |
|---|---|---|---|
| **Reusable prompt** | `.github/prompts/*.prompt.md`. You run these manually, and they suit focused single tasks you run once with different inputs. | There is no separate type. Custom slash commands have been merged into skills, so `.claude/commands/*.md` and skills both create a `/name` command and work the same way. | No equivalent. Prompting is part of the agent definition. |
| **Skills** | `.github/skills/<name>/SKILL.md`. Copilot chooses them automatically when relevant to your prompt. It also reads `.claude/skills/` and `.agents/skills/`. | Personal skills go in `~/.claude/skills/<name>/SKILL.md`, project skills in `.claude/skills/<name>/SKILL.md`, and plugin skills in `<plugin>/skills/<name>/SKILL.md`. Only the frontmatter `description` sits in context until the skill triggers, then the body loads, then any bundled files on demand. Frontmatter can also set `allowed-tools`, hooks, and whether the model or only you can invoke it. | Skills package reusable multi-step workflows and are versioned and immutable. Agents discover and load them automatically through MCP resources at startup. They sit in a project-scoped catalog, so any agent in the project can find them. This is a preview feature. |
| **Custom agent** | `.github/agents/*.agent.md`. A specialist persona with its own instructions, tool restrictions, and context. You pick it from the agent dropdown, and handoffs chain agents. | `.claude/agents/*.md` define subagents. Each runs in its own context window with its own system prompt, tool allowlist, and model. Frontmatter supports `disallowedTools`, `permissionMode`, `mcpServers`, `hooks`, `skills`, `maxTurns`, `memory`, `model`, and more. Claude can delegate to one automatically based on its description, and you manage them with `/agents`. | Two kinds. A **prompt agent** is defined entirely through prompts and tool configuration in the portal. A **hosted agent** is your own code packaged as a container image. |

## 3. Tools and enforcement

| | Copilot (VS Code) | Claude Code | Foundry |
|---|---|---|---|
| **External tools** | MCP servers plus the tool picker in chat, with per-agent tool lists. | MCP servers in `.mcp.json` (project) or user config, added with `claude mcp add`. Permission rules in `settings.json` allow, ask, or deny specific tools and commands, for example `Bash(dotnet test:*)`. | A toolbox is a curated bundle of tools such as web search, Azure AI Search, code interpreter, file search, MCP servers, and OpenAPI tools, configured once and exposed as a single MCP-compatible endpoint. |
| **Changing tools without touching the agent** | You edit the agent file or the MCP config. | You edit the config file. | A toolbox is a managed resource, so you can add, remove, or update tools without changing agent code. Every agent pointing at it picks up the promoted version automatically. |
| **Many-tools problem** | You curate the tool list per agent. | Subagents and skills limit what is loaded into the main context. | Tool search hides tools by default and exposes only two meta-tools, including `call_tool`, which invokes any discovered tool by name. It's a preview feature. |
| **Deterministic control** | Limited. Instructions are advisory. | **Hooks** are shell commands fired on events such as `PreToolUse`, `PostToolUse`, `SessionStart`, and `Stop`. They run outside the model, so they can block a tool call or run a formatter every time. Claude has no awareness of hooks. | Content filters cover harm categories, Prompt Shields for jailbreak and prompt injection, groundedness, PII, and protected material. These are platform-level rather than per-agent scripts. |

## 4. Scope, lifecycle, and tuning

| | Copilot (VS Code) | Claude Code | Foundry |
|---|---|---|---|
| **Where config lives** | Repo `.github/`, your user profile, or org-level settings on GitHub. | Repo `.claude/`, `~/.claude/`, plugins, and managed policy files for organizations. Settings precedence runs managed, then command line, then local, then project, then user. | A Foundry project. You edit it in the portal, the SDK, or `azd`. |
| **Versioning** | Git history. | Git history for the project files. | The platform versions it. A tutorial's SDK sample calls `project.agents.create_version`. Toolbox edits made in Agent Builder are staged until you save, which creates a new toolbox version. |
| **Sharing** | Commit the files, or publish org-level agents in a `.github-private` repo. | Commit `.claude/settings.json`, `rules/`, `skills/`, and `agents/`. Plugins and marketplaces package everything together. | Everyone with project access shares the same resources. A toolbox can be shared across agents. |
| **Agent-to-agent** | Handoffs between agents inside a chat. | Subagents report back to the main session. Agent teams are a newer addition. | Outbound A2A has been supported since the A2A tool launched. Incoming A2A (public preview) lets you expose any Foundry agent as an A2A endpoint. |
| **Identity and runtime** | Runs as you, in your IDE. | Runs as you, locally, sandboxed by permissions. | Every hosted agent gets its own Entra ID (agent identity) and a dedicated endpoint, both created at deploy time. Hosted agents run in per-session VM-isolated sandboxes. |
| **Improving prompts** | You iterate by hand. | You iterate by hand, or ask Claude to refine the file. | A prompt optimizer improves instructions automatically from evaluation results, and an agent optimizer (preview) tunes hosted-agent instructions and skills against a target dataset. |

## Things worth knowing coming from Copilot

- **Claude Code is the closest match.** The Copilot file types map onto it almost one to one. The big additions are **hooks** (enforcement outside the model) and **subagents with isolated context windows**.
- **Foundry is a different category.** You're configuring a deployed agent, so its settings are resources with versions, identities, and RBAC. For a .NET developer on Azure, hosted agents are probably the interesting part: you containerize your own code and Foundry runs it behind an endpoint.
- **Prompt agents use a toolbox or individual tools, not both.** Attaching a toolbox replaces the individual tools already attached to the agent. Skills and tool search only work through a toolbox.
- **Maturity labels vary.** One docs page says the tool catalog and toolboxes are generally available, but another still labels toolbox as preview. Check the current status before relying on it.

If you want, I can go deeper on one column: an example `.claude/agents/` file and hook for a .NET repo, or a prompt agent versus hosted agent walkthrough in Foundry.
