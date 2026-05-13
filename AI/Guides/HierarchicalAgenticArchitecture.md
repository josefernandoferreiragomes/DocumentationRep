
# Guide to AI-Powered Agentic Software Development Workflows

This guide outlines the hierarchical workflow for using AI agents within IDEs like VS Code (via tools like Claude Code, Open Code, or AutoGen). It focuses on managing context and complexity through a modular structure of Agents, Sub-Agents, Skills, and Tools.

---

## 1. The Workflow Hierarchy

To maintain a "clean" context window and prevent "context rot," the project is organized into layers:

### A. Project Context (`AGENTS.md` / `.instructions.md`)
The "Source of Truth." This file is always in the agent's memory.
- **Contents:** Tech stack (e.g., "React + Vite + Bun"), naming conventions, and project goals.
- **Purpose:** Prevents you from repeating basic instructions in every prompt.

### B. Primary Agents
High-level orchestrators (the "Chefs").
- **Roles:** Architect, Coder, or Reviewer.
- **Functions:** They plan tasks and decide when to delegate to specialized sub-agents.

### C. Sub-Agents (Sous Chefs)
Disposable reasoning modules for narrow tasks.
- **Use Case:** "Researcher" scans files; "Test Runner" fixes broken tests.
- **Benefit:** They operate in a separate thread, returning only a summary to the main agent to save tokens and focus.

### D. Skills (`SKILL.md`)
Modular "recipe cards" for the agent.
- **Example:** A `security-audit.md` skill that contains a checklist for scanning vulnerabilities.
- **Mechanism:** The agent "loads" the full skill instructions only when a specific task triggers it.

### E. Tools & MCP (Model Context Protocol)
The "Hands" of the agent.
- **Tools:** Bash terminal, file read/write, and git commands.
- **MCP:** A standard protocol that lets agents connect to external apps (e.g., searching Google, pulling live Docs, or interacting with a running UI).

---

## 2. Step-by-Step Implementation

1. **Initialize Context:** Create an `AGENTS.md` file in your root. Define your environment (e.g., "Always use TypeScript," "Unit tests go in /tests").
2. **Define Skills:** For recurring complex tasks (like building the project), create a `.skills/build.md` file. 
3. **Execute via Commands:** Use slash commands (e.g., `/create-pr`) to trigger repeatable workflows like summarizing changes and pushing to GitHub.
4. **Delegate to Sub-Agents:** When a task involves high "noise" (like reading 50 error logs), instruct the agent to "Spawn a sub-agent to analyze these logs and just give me the fix."

---

## 3. Official References & Technical Foundations

### Microsoft Magentic-One & AutoGen
Microsoft's official architecture for "Generalist Multi-Agent Systems." It defines the Orchestrator-Worker relationship.
- **Reference:** *Fourney, A., et al. (2024). Magentic-One: A generalist multi-agent system for solving complex tasks.* [arXiv:2411.04468]

### Context Rot & Token Management
Research from Anthropic and others emphasizes that AI performance degrades as the context window fills. The hierarchical workflow is a direct solution to this "Needle in a Haystack" problem.
- **Reference:** *Damarched, M. K. (2026). Agentic AI modernization: Transforming institutional infrastructure.*

Piskala, D. B. (2026). Agent, sub-agent, skill, or tool? A practitioner's guide to extending agentic AI systems. TechRxiv. https://doi.org/10.36227/techrxiv.177204917.78786098

[https://d3s.mff.cuni.cz/f/publications/tr-2026-01.pdf](https://d3s.mff.cuni.cz/f/publications/tr-2026-01.pdf)
Bureš, T. (2026). Component-based development for the era of AI coding agents. Department of Distributed and Dependable Systems.

Fourney, A., Bansal, G., Mozannar, H., Tan, C., Salinas, E., Erkang, Zhu, Niedtner, F., Proebsting, G., Bassman, G., Gerrits, J., Alber, J., Chang, P., Loynd, R., West, R., Dibia, V., Awadallah, A., Kamar, E., Hosn, R., & Amershi, S. (2024). Magentic-One: A generalist multi-agent system for solving complex tasks. arXiv.
[https://doi.org/10.48550/arxiv.2411.04468](https://doi.org/10.48550/arxiv.2411.04468)

### Recommended Tutorials
- **Advanced Agentic Guide:** [AI Coding Agents: Subagents, Skills, MCP](https://www.youtube.com/watch?v=DAaw7Ao_zUc) (pookie, 2026).
- **Toolchain Explanation:** [AI Agentic Toolchains Explained](https://www.youtube.com/watch?v=s0DyKinw174) (Uno Platform, 2026).

---
