# Agent team

Mona's Project Pulse dashboard will be built by a specialist team coordinated through the GitHub Copilot CLI in this Codespace. The Orchestrator will turn the request into sequenced, non-overlapping assignments: the Planner researches and defines the delivery plan first, then the Coder and Designer implement their assigned logic and experience work. The learner retains control of all staging, commits, and pushes.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Coordinates the team from Copilot CLI; converts the plan into dependency-aware phases, assigns explicit file scopes, runs independent work in parallel, integrates the result, and reports blockers or outcomes. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, risks, and edge cases; returns an ordered implementation plan with file ownership, dependencies, parallelism, and validation expectations. Does not write code. | `.github/agents/planner.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements dashboard behavior and supporting runnable-app configuration within its assigned files, using explicit errors and deterministic, testable code. For Project Pulse, it can create the prescribed VS Code launch configuration when assigned. | `.github/agents/coder.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, accessibility, information hierarchy, interaction flow, responsive behavior, and visual polish, including clear project cards, status badges, priority treatment, and stable CSS hooks. | `.github/agents/designer.agent.md` |

Each specialist stays inside the file scope assigned by the Orchestrator and does not perform Git operations; GitHub Copilot CLI prompts remain the control point for repository changes.
