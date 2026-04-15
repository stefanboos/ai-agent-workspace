# 001: AI Agent Rule Adherence Strategy

- Status: Accepted
- Date: 2025-10-11

## Executive Summary

This document records the decision-making process for a strategy to solve
the problem of "AI rule degradation," where AI coding agents forget
foundational rules during long sessions. The goal is to choose a solution
that improves the agent's reliability, reduces the developer's cognitive
load, and maintains a smooth workflow. This ADR analyzes potential
solutions and evaluates them against the key decision drivers to arrive at
a well-reasoned architectural choice.

## Context and Problem Statement

AI agents exhibit rule degradation over extended development sessions. As
session context grows, agents tend to "forget" foundational rules,
particularly during critical phases like implementation (e.g., TDD
principles) and version control (e.g., commit message formatting, memory
bank updates). This leads to inconsistent quality, workflow disruptions,
and increased cognitive load for the developer who must manually review
and correct these lapses.

The root causes stem from fundamental limitations in current AI
architecture, which are detailed in the appendix.

This architectural decision record (ADR) is intended for project
stakeholders, including the developer (Stefan) and any future
contributors. It explores potential solutions to ensure AI agents reliably
adhere to predefined rules throughout their operational lifecycle.

## Decision Drivers

The selection of a solution will be guided by the following key
principles, prioritized for a solo developer workflow:

- 🚀 **Rapid Implementation**: The solution must be simple enough to
  implement and provide value in under two weeks.
- 🛠️ **Low Maintenance Overhead**: Once configured, the solution should
  require minimal ongoing tuning, management, or manual intervention.
- 🧘 **Minimal Workflow Disruption**: The solution should integrate
  seamlessly into the existing workflow without imposing blocking
  interruptions or significant friction.

## Considered Options

1. **Context-Aware Rule Reinforcement**: A system that intelligently
   reminds the agent of the rules at opportune moments (e.g., based on
   session duration or specific triggers like `git commit`). See
   [Detailed Analysis](#1-context-aware-rule-reinforcement).
2. **Hybrid Memory Architecture**: A more complex, persistent memory
   system that gives the agent long-term knowledge of the rules, separate
   from its working (session) memory. See
   [Detailed Analysis](#2-hybrid-memory-architecture).
3. **Intelligent Rule Validation Pipeline**: A system that validates the
   agent's output (e.g., a commit message) against the rules and blocks
   non-compliant actions, often integrated with pre-commit hooks. See
   [Detailed Analysis](#3-intelligent-rule-validation-pipeline).
4. **AI-Assisted Memory Bank Enforcement**: A specialized validation
   system that uses semantic analysis to enforce the structure and content
   quality of the project's memory bank. See
   [Detailed Analysis](#4-ai-assisted-memory-bank-enforcement).
5. **Session-Aware Workflow Integration**: A deeply integrated system
   that embeds rule compliance checks into the natural development
   workflow, adapting to the developer's patterns. See
   [Detailed Analysis](#5-session-aware-workflow-integration).
6. **Do Nothing (Accept the Status Quo)**: Make no changes and continue
   with the current manual review and correction process. See
   [Detailed Analysis](#6-do-nothing-accept-the-status-quo).
7. **Structured Manual Workarounds**: Formalize manual processes like
   re-pasting rules or using checklists to improve compliance. See
   [Detailed Analysis](#7-structured-manual-workarounds).
8. **Buy a Commercial Off-The-Shelf (COTS) Product**: Research and
   purchase an existing product that solves the problem. See
   [Detailed Analysis](#8-buy-a-commercial-off-the-shelf-cots-product).
9. **Agent Skills (Automatic Rule Re-Enforcement)**: An open standard
   for on-demand skill loading where rules and workflows are packaged as
   `SKILL.md` files that agents discover and load automatically at the
   right moment. See
   [Detailed Analysis](#9-agent-skills-automatic-rule-re-enforcement).

## Options Comparison Matrix

The following table provides a high-level comparison of the considered
options against the prioritized decision drivers. It also includes an
"Effectiveness" score that rates how well each option solves the core
problem of AI rule degradation on a scale of 0 to 4:

- **0**: No solution at all
- **1**: Barely addresses the problem
- **2**: A partial solution or a component of a larger solution
- **3**: An effective solution that solves the core problem
- **4**: An ideal, fully-automated solution

| | Effectiveness | 🚀 Rapid Implementation | 🛠️ Low Maintenance | 🧘 Minimal Disruption |
| :--- | :---: | :---: | :---: | :---: |
| **Context-Aware Rule Reinforcement** | 3 | ✅ | ✅ | ✅ |
| **Hybrid Memory Architecture** | 4 | ❌ | ❌ | ✅ |
| **Intelligent Rule Validation Pipeline** | 3 | ❌ | ⚠️ | ❌ |
| **AI-Assisted Memory Bank Enforcement** | 2 | ⚠️ | ⚠️ | ❌ |
| **Session-Aware Workflow Integration** | 4 | ❌ | ❌ | ⚠️ |
| **Do Nothing** | 0 | ✅ | ✅ | ✅ |
| **Structured Manual Workarounds** | 2 | ✅ | ✅ | ⚠️ |
| **Buy a COTS Product** | 2 | ⚠️ | ✅ | ⚠️ |
| -> **Agent Skills** | 4 | ✅ | ✅ | ✅ |

**Legend**: ✅ = Positive Alignment, ⚠️ = Neutral/Mixed Alignment,
❌ = Negative Alignment

## Decision Outcome and Consequences

**Chosen Option:** [9. Agent Skills (Automatic Rule Re-Enforcement)][o9]

[o9]: #9-agent-skills-automatic-rule-re-enforcement

### Rationale

This decision evolved through two validated iterations before arriving at
the current solution.

**Iteration 1 — COTS (Cursor IDE `always_apply`)**: Initial trials showed
that always-on rule injection provides a useful baseline but is
insufficient for long sessions. Rules loaded at session start are
gradually diluted as context grows.

**Iteration 2 — Manual Context-Aware Rule Reinforcement**: A structured
prompt library proved genuinely effective at re-introducing rules at
critical workflow moments. This eliminated the worst violations and
reduced cognitive load significantly. However, it introduced a new
dependency: the developer must remember to invoke the right prompt at the
right time. Over time, this discipline erodes, and the agentic tooling
landscape evolved faster than the prompt library could be maintained.

**Iteration 3 — Agent Skills**: The Agent Skills open standard
(agentskills.io) automates exactly what the manual prompt library
achieved. Rules and workflows are packaged as `SKILL.md` files. The agent
loads them on-demand — either automatically when its description matches
the current task, or on explicit invocation via `/skill-name`. Two
properties make this structurally superior to previous approaches:

- ✅ **On-demand loading**: Skill content enters the context only when
  needed, avoiding the dilution that defeats always-on injection.
- ✅ **Compaction persistence**: After context compaction, Claude Code
  re-attaches the most recently invoked skills, so rules survive long
  sessions without manual re-introduction.

This repository already validates the approach in practice: skills such as
`/commit`, `/memory-update`, `/memory-create`, and `/memory-to-docs`
package the exact workflows that were previously maintained as a manual
prompt library.

The standard is tool-agnostic (adopted by Claude Code, Cursor, GitHub
Copilot, Gemini CLI, and 30+ others), which removes the vendor lock-in
risk identified in Iteration 1.

### Consequences

- **Positive**:
  - Rule re-enforcement is fully automatic — no developer discipline
    required to invoke the right prompt.
  - Skills survive context compaction, directly addressing the root
    cause of long-session degradation.
  - The same skill files work across multiple agent tools, eliminating
    vendor lock-in.
  - Packaging workflows as skills is lightweight and incremental: one
    `SKILL.md` file per workflow.
- **Negative**:
  - Skill descriptions must be written carefully so the agent knows
    when to auto-invoke them. Vague descriptions lead to missed or
    unnecessary invocations.
  - After compaction, only the first 5,000 tokens of each skill are
    re-attached, with a shared budget of 25,000 tokens across all
    skills. Very large skill sets may require prioritization.
- **Risks**:
  - The quality of rule enforcement depends on the quality of the
    `SKILL.md` content. Skills still need to be authored and
    occasionally reviewed.
  - As the skills ecosystem matures, the format or tooling may evolve.
    The open standard mitigates this, but migrations may occasionally
    be needed.

## Links

- [Project Brief (Memory Bank)](../memory-bank/projectbrief.md)
- [System Patterns (Memory Bank)](../memory-bank/systemPatterns.md)

## Appendix: Detailed Option Analysis and Root Causes

### Root Cause Analysis

The core problem of AI rule degradation stems from three fundamental
limitations in current AI architecture:

- **Context Window Exhaustion**: Earlier instructions are pushed out of
  the model's limited context window as the conversation grows.
- **Attention Dilution**: The agent's focus gets distributed across too
  many tokens, weakening the signal of the original rules compared to
  more recent instructions.
- **Lack of Persistent Memory**: Agents are stateless and do not retain
  memory of rules across sessions without external mechanisms.

### 1. Context-Aware Rule Reinforcement

- **Description**: This approach focuses on reminding the agent of the
  rules at the right time. The trigger can be simple (e.g., every N
  commits) or more intelligent (e.g., detecting signs of deviation).
- **Pros**:
  - ✅ Aligns perfectly with all decision drivers: fast to implement,
    non-disruptive, and low maintenance.
  - ✅ Directly addresses the "forgetting" problem by refreshing the
    agent's context.
  - ✅ Can be implemented incrementally, starting with a very simple
    MVP.
- **Cons**:
  - ❌ May not prevent 100% of violations if the timing of the reminder
    isn't perfect.
  - ❌ A naive implementation could become noisy if not tuned correctly.
- **Mitigations**:
  - To improve accuracy, the simple reminder can be combined with a
    lightweight, non-blocking validation check for critical tasks.
  - To reduce noise, the system can use adaptive timing for reminders
    based on session complexity or allow the developer to tune the
    frequency.

### 2. Hybrid Memory Architecture

- **Description**: This involves building a long-term memory for the
  agent where rules are permanently stored and retrieved, separate from
  the chat history.
- **Pros**:
  - ✅ Offers a robust, long-term solution to rule persistence.
- **Cons**:
  - ❌ Violates **Rapid Implementation**: Requires significant
    architectural work.
  - ❌ Violates **Low Maintenance**: Such a system would be complex to
    build and maintain.
  - ❌ Could be overkill for a solo developer context.
- **Mitigations**:
  - The implementation complexity can be reduced by starting with a
    simple key-value store and focusing the MVP on storing only the
    most critical rules.
  - Using existing libraries (e.g., LangChain memory modules) can
    abstract away some of the maintenance burden.

### 3. Intelligent Rule Validation Pipeline

- **Description**: This system acts as a gatekeeper, validating the
  agent's outputs against rules before an action (like a commit) is
  completed.
- **Pros**:
  - ✅ Provides the strongest guarantee of rule compliance.
- **Cons**:
  - ❌ Violates **Minimal Disruption**: Inherently blocking and can
    cause workflow friction.
  - ❌ Violates **Rapid Implementation**: Building a reliable and
    non-annoying validation pipeline is complex.
  - ❌ Can lead to "fighting the linter" scenarios, which is a poor
    user experience.
- **Mitigations**:
  - To minimize disruption, the pipeline can be configured to be
    non-blocking by default, showing warnings instead of errors for
    most violations.
  - The user experience can be improved by providing clear, actionable
    feedback and allowing developers to easily disable specific rules.

### 4. AI-Assisted Memory Bank Enforcement

- **Description**: A specialized version of the validation pipeline
  focused solely on ensuring the memory bank's integrity.
- **Pros**:
  - ✅ Solves a specific, high-pain point (memory bank content
    duplication and structure).
- **Cons**:
  - ❌ Shares the same "disruption" and "complexity" cons as the
    general validation pipeline.
  - ❌ Only solves part of the problem, as it doesn't address
    rule-breaking in other areas like commit messages.
- **Mitigations**:
  - To reduce disruption, this can be implemented as a background
    process that provides suggestions rather than a blocking pre-commit
    hook.
  - The limited scope can be addressed by integrating it as one
    component of a larger, more comprehensive solution.

### 5. Session-Aware Workflow Integration

- **Description**: The most advanced option, where rule awareness is
  woven deeply into the IDE and development process.
- **Pros**:
  - ✅ Potentially offers the most seamless and intelligent user
    experience.
- **Cons**:
  - ❌ Violates all three decision drivers: it is extremely complex,
    slow to implement, and would require significant maintenance. This
    is a large-scale project, not a targeted solution.
- **Mitigations**:
  - The complexity can be managed by scoping the MVP down to a single,
    small integration point (e.g., providing rule hints in the IDE for
    `.md` files).
  - The system can be built as a series of independent micro-tools
    rather than a monolithic system to reduce the maintenance burden.

### 6. Do Nothing (Accept the Status Quo)

- **Description**: This option involves making no changes to the
  current workflow. The developer continues to manually monitor the AI
  agent's output, provide reminders, and correct mistakes as they
  occur.
- **Pros**:
  - ✅ **Zero Implementation Effort**: Requires no time or resources to
    implement, making it the cheapest option in the short term.
  - ✅ **No Workflow Disruption**: The existing workflow is maintained
    without introducing new tools or processes that could cause
    friction.
- **Cons**:
  - ❌ **No Improvement**: The core problem of rule degradation remains
    unaddressed, leading to continued frustration and wasted time.
  - ❌ **High Cognitive Load**: Places a constant burden on the
    developer to act as the "rule enforcer," especially at the end of
    sessions when focus is low.
  - ❌ **Inconsistent Quality**: The quality of the output will
    continue to vary depending on the agent's state and the developer's
    diligence in catching errors.
  - ❌ **Higher Long-Term Cost**: The cumulative time spent on manual
    correction and rework will likely exceed the up-front investment of
    an automated solution.
- **Mitigations**:
  - By definition, this option has no mitigations, as implementing
    them would mean choosing a different option. The negative
    consequences are inherent to this choice.

### 7. Structured Manual Workarounds

- **Description**: This option formalizes the manual processes the
  developer can use to improve AI agent compliance. It relies on the
  developer's discipline rather than automated enforcement and includes
  techniques like systematically re-pasting rules before critical tasks
  or using manual checklists.
- **Pros**:
  - ✅ **No Implementation Cost**: Requires no coding or setup.
  - ✅ **High Flexibility**: The developer can choose which workarounds
    to apply and when, adapting to the specific situation.
  - ✅ **Can be Effective**: With discipline, these methods can
    significantly improve the agent's adherence to rules.
- **Cons**:
  - ❌ **Shifts Burden to User**: This approach makes the developer
    responsible for remembering and executing the workarounds, which is
    the very problem the project aims to solve.
  - ❌ **High Cognitive Load**: It replaces the cognitive load of
    "reviewing and correcting" with that of "remembering and applying
    workarounds."
  - ❌ **Inconsistent and Error-Prone**: The effectiveness depends
    entirely on the developer's consistency. Forgetting to apply a
    workaround leads back to the original problem.
  - ❌ **Does Not Scale**: This is not a sustainable long-term solution
    and does not address the root cause of AI rule degradation.
- **Mitigations**:
  - The cognitive load and error rate can be reduced by using
    templates, editor snippets, and physical checklists to make the
    workarounds easier to apply.

### 8. Buy a Commercial Off-The-Shelf (COTS) Product

- **Description**: This option involves researching and purchasing an
  existing commercial product that provides AI agent management, rule
  enforcement, or a similar capability.
- **Pros**:
  - ✅ **Potential for Rapid Solution**: If a suitable product exists,
    it could be faster than building a custom solution.
  - ✅ **Leverages External Expertise**: A commercial product would be
    built and maintained by a dedicated team, saving internal
    development and maintenance effort.
- **Cons**:
  - ❌ **Market Unlikely to Exist for Solo Developers**: The market for
    this specific problem (a solo developer managing their AI agent's
    rules) is likely nonexistent. Products in this space are typically
    enterprise-focused and expensive.
  - ❌ **High Cost**: Commercial AI solutions often involve
    subscription fees that may not be justifiable for a solo developer.
  - ❌ **Lack of Customization**: A commercial product may not be
    flexible enough to integrate with a highly customized workflow.
  - ❌ **No Obvious Candidates**: A preliminary search reveals no
    ready-to-buy products that solve this specific problem for an
    individual developer.
- **Mitigations**:
  - The lack of a direct solution can be mitigated by exploring
    adjacent markets (e.g., developer productivity tools) or
    open-source alternatives.
  - High costs and lack of customization can be addressed by using a
    free trial to thoroughly evaluate the product's flexibility before
    purchase.

#### 8.1. Specific Option: Adopt Cursor IDE

- **Description**: This sub-option involves adopting the
  [Cursor IDE](https://cursor.com/), an AI-native code editor. Cursor
  has a built-in "Rules" feature that automatically injects
  user-defined instructions into the AI's context before every request.
  It specifically includes an "always apply" setting, which directly
  addresses the problem of rule degradation by ensuring foundational
  rules are never forgotten.
- **Pros**:
  - ✅ **Provides Baseline Context**: The "always apply" feature is
    effective at loading the rules at the start of a session, providing
    a solid foundation for the agent.
  - ✅ **Excellent Alignment with Decision Drivers**: It offers a very
    **Rapid Implementation** (install and configure), requires **Low
    Maintenance** (vendor-supported feature), and has **Minimal
    Disruption** once the initial IDE switch is made.
  - ✅ **Portable Rule Content**: The rules are written in standard
    Markdown, making them easy to migrate to or from other systems if
    needed.
- **Cons**:
  - ❌ **Insufficient as a Standalone Solution**: Validation has shown
    that this feature alone does not prevent rule degradation over long
    sessions, as the rules are not re-loaded.
  - ❌ **Vendor Lock-in for Enforcement**: While the rule *content* is
    portable, the automated *enforcement mechanism* is exclusive to the
    Cursor IDE. Using other AI tools would still require manual rule
    application.
  - ❌ **Requires Workflow Migration**: Adopting this solution
    necessitates switching the primary development environment to
    Cursor, which could involve a learning curve and configuration
    effort.
- **Mitigations**:
  - The insufficiency of the feature is mitigated by using it in
    combination with the **Context-Aware Rule Reinforcement** strategy
    (i.e., the prompt-driven workflow).
  - The vendor lock-in is mitigated by the user's acceptance of having
    one primary automated tool and a manual process for others.
  - The workflow migration effort can be minimized by leveraging
    Cursor's compatibility with VS Code extensions and settings.

### 9. Agent Skills (Automatic Rule Re-Enforcement)

- **Description**: Rules, checklists, and workflow playbooks are
  packaged as `SKILL.md` files in a `.claude/skills/` directory (or the
  equivalent for other tools). The agent reads skill `description`
  fields at session start and automatically loads the full content when
  the current task matches. Developers can also invoke skills directly
  with `/skill-name`. After context compaction, Claude Code re-attaches
  the most recently invoked skills automatically.
- **Pros**:
  - ✅ **Automatic invocation**: No developer discipline required — the
    agent decides when a skill is relevant based on the `description`
    field.
  - ✅ **On-demand loading**: Skill content is not in the context until
    needed, so it does not contribute to dilution or context waste.
  - ✅ **Compaction persistence**: Re-attaching invoked skills after
    compaction directly addresses long-session rule degradation at the
    infrastructure level.
  - ✅ **Tool-agnostic**: The Agent Skills open standard
    (agentskills.io) is supported by Claude Code, Cursor, GitHub
    Copilot, Gemini CLI, and 30+ other tools. Skills written for one
    tool are portable.
  - ✅ **Aligns with all decision drivers**: Skills are trivial to
    create (rapid implementation), require no ongoing infrastructure
    (low maintenance), and integrate silently into the workflow
    (minimal disruption).
- **Cons**:
  - ❌ **Description quality matters**: If a skill's description does
    not match how the developer naturally phrases requests, the agent
    will miss or over-trigger it.
  - ❌ **Compaction token budget**: Re-attached skills share a
    25,000-token budget. Large or numerous skills may not all survive
    compaction.
- **Mitigations**:
  - Write descriptions that front-load the key use case and include
    natural trigger phrases. Test by asking "What skills are
    available?" and reviewing whether the description would match
    common requests.
  - Keep individual `SKILL.md` files focused and under 500 lines. Move
    detailed reference material to supporting files referenced from the
    skill.

### Glossary

- **ADR (Architectural Decision Record)**: A document that captures an
  important architectural decision, including its context, drivers, and
  consequences.
- **Context Window**: The limited amount of text an AI model can "see"
  at one time to generate a response.
- **TDD (Test-Driven Development)**: A software development process
  where tests are written before the code that they are intended to
  validate.
- **LangChain**: A framework for developing applications powered by
  language models.
