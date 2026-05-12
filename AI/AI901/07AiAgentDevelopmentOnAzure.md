# AI Agent Fundamentals on Azure

# Chapter 1 — Introduction to AI Agent Fundamentals

## Overview
AI agents are intelligent systems capable of:
- reasoning
- planning
- taking actions
- interacting with users and external systems
- operating semi-autonomously or autonomously

Microsoft positions agents as the next evolution of generative AI applications built on:
- Large Language Models (LLMs)
- orchestration frameworks
- external tools/services
- memory and contextual grounding

The module focuses on:
- core agent concepts
- development approaches
- Azure AI Foundry Agent Service

---

## What Makes an AI Agent Different from a Traditional AI Application

| Traditional AI App | AI Agent |
|---|---|
| Usually single-step inference | Multi-step reasoning and execution |
| Reactive | Goal-driven |
| Limited external interaction | Uses tools, APIs, data sources |
| User controls flow | Agent can plan actions |
| Stateless in many scenarios | Often maintains memory/context |

---

## Core Capabilities of AI Agents

### 1. Reasoning
Agents use LLMs to:
- interpret requests
- determine intent
- decide next actions
- chain operations together

### 2. Tool Usage
Agents can invoke:
- APIs
- databases
- search systems
- enterprise systems
- plugins/functions

This is critical for grounding and real-world usefulness.

### 3. Memory
Agents may maintain:
- conversation context
- user preferences
- task state
- retrieved knowledge

### 4. Planning
Advanced agents:
- decompose tasks
- determine execution order
- adapt dynamically

### 5. Autonomy
Agents can:
- execute tasks independently
- operate with limited human intervention
- react to changing conditions

---

## Key Takeaways

- AI agents extend LLMs with planning, memory, and tool usage.
- Agents are goal-driven and capable of multi-step execution.
- Azure AI Foundry provides infrastructure for agent development.
- Agentic AI emphasizes autonomous reasoning and orchestration.
- Agents differ significantly from basic conversational bots.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/ai-agent-fundamentals/
- https://learn.microsoft.com/en-us/azure/ai-foundry/

---
---

# Chapter 2 — What Are AI Agents?

## Definition

An AI agent is a system that:
- perceives input
- reasons about objectives
- selects actions
- executes tasks using tools or systems

Agents combine:
- LLMs
- orchestration logic
- memory
- external integrations

---

## Agent Components

| Component | Purpose |
|---|---|
| Model | Reasoning and language generation |
| Prompt/System Instructions | Behavioral guidance |
| Memory | Context persistence |
| Tools | External capabilities |
| Orchestrator | Controls execution flow |
| Knowledge Sources | Grounding data |

---

## Tool Calling

A critical capability.

Agents can:
- execute functions
- call REST APIs
- query databases
- retrieve documents
- send emails/messages

This bridges LLM reasoning with real-world systems.

---

## Retrieval-Augmented Generation (RAG)

Many agents use RAG to:
- retrieve enterprise data
- ground responses in factual information
- reduce hallucinations

Typical architecture:
1. User query
2. Search/retrieval
3. Context injection
4. LLM response generation

---

## Key Takeaways

- AI agents combine reasoning, memory, orchestration, and tools.
- Tool calling is fundamental to practical agents.
- RAG improves grounding and factual accuracy.
- Agents may range from simple assistants to autonomous systems.
- Responsible AI is critical for agent deployment.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/ai-agent-fundamentals/2-what-are-agents
- https://learn.microsoft.com/en-us/azure/ai-foundry/

---
---

# Chapter 3 — Options for Agent Development

## Overview

Microsoft provides multiple approaches for building AI agents depending on:
- complexity
- control requirements
- coding expertise
- enterprise integration needs

---

## Development Approaches

| Approach | Characteristics |
|---|---|
| Low-code | Rapid development with visual tooling |
| SDK-based | Full developer control |
| Framework-based | Modular orchestration and extensibility |
| Managed agent services | Hosted enterprise-ready infrastructure |

---

## Semantic Kernel

Microsoft’s orchestration framework for AI applications.

Key capabilities:
- plugin/tool management
- memory integration
- planning
- prompt templating
- multi-agent orchestration

---

## Security and Governance

Enterprise agent systems require:
- RBAC
- API security
- managed identities
- audit logging
- responsible AI controls

---

## Key Takeaways

- Multiple development paths exist for AI agents.
- Semantic Kernel is Microsoft’s orchestration framework.
- Azure AI Foundry centralizes agent development capabilities.
- Enterprise agents require monitoring, security, and governance.
- Multi-agent systems improve specialization but add complexity.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/ai-agent-fundamentals/3-agent-development
- https://learn.microsoft.com/en-us/semantic-kernel/overview/
- https://learn.microsoft.com/en-us/azure/ai-foundry/

---
---

# Chapter 4 — Azure AI Foundry Agent Service

## Overview

Azure AI Foundry Agent Service is Microsoft’s managed service for:
- building
- deploying
- orchestrating
- monitoring AI agents

It abstracts much of the infrastructure complexity involved in agent systems.

---

## Main Capabilities

| Capability | Purpose |
|---|---|
| Agent orchestration | Manage workflows and execution |
| Tool integration | Connect APIs and external systems |
| Model integration | Use Azure OpenAI and other models |
| Memory/context handling | Maintain conversation/task state |
| Monitoring | Observe agent behavior |
| Security | Enterprise-grade governance |

---

## Grounding and Retrieval

Agents often use:
- vector search
- enterprise document retrieval
- RAG pipelines

Purpose:
- factual accuracy
- enterprise relevance
- reduced hallucinations

---

## Key Takeaways

- Azure AI Foundry Agent Service is a managed platform for AI agents.
- It supports orchestration, tools, memory, and monitoring.
- Grounding and retrieval improve factual reliability.
- Enterprise security and governance are essential.
- Agent services simplify production AI agent deployment.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/ai-agent-fundamentals/4-azure-ai-agent-service
- https://learn.microsoft.com/en-us/azure/ai-foundry/
- https://learn.microsoft.com/en-us/training/modules/develop-ai-agent-azure/
