# Agent team for Mona's Project Pulse dashboard

I will use a small custom agent team to orchestrate the build of Mona's Project Pulse dashboard from the GitHub Copilot CLI in a Codespace.

- Orchestrator — Model: Claude Opus 4.7 (copilot). Responsible for breaking the project into phases, delegating work to specialist agents, and coordinating execution without implementing the code directly. Definition: `.github/agents/orchestrator.agent.md`.
- Planner — Model: Claude Opus 4.7 (copilot). Responsible for researching the repo, documenting constraints, and creating a practical implementation plan with file assignments, sequencing, dependencies, validation expectations, and open questions. Definition: `.github/agents/planner.agent.md`.
- Designer — Model: Gemini 3.1 Pro (copilot). Responsible for the UX, accessibility, information hierarchy, responsive layout, and visual styling for the Project Pulse dashboard so it feels like a polished product rather than a bare prototype. Definition: `.github/agents/designer.agent.md`.
- Coder — Model: GPT-5.5 (copilot). Responsible for implementing the actual code, fixing bugs, and validating behavior within the file scope assigned by the Orchestrator. Definition: `.github/agents/coder.agent.md`.

The team is defined under the repository's `.github/agents` folder and is coordinated through GitHub Copilot CLI running in a Codespace, with the Orchestrator acting as the central coordinator for planning, delegation, and review.
