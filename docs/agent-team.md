# Agent team

I will use this custom agent team to build Mona's Project Pulse dashboard, coordinated through GitHub Copilot CLI in a Codespace:

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| Orchestrator | Claude Opus 4.7 (copilot) | Breaks down the work, assigns scoped tasks to specialists, coordinates dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| Planner | Claude Opus 4.7 (copilot) | Researches the repository and relevant tools, then provides an implementation plan with file assignments, dependencies, risks, and validation expectations. | `.github/agents/planner.agent.md` |
| Designer | Gemini 3.1 Pro (copilot) | Defines and implements the dashboard's user experience and visual design, with attention to accessibility, hierarchy, responsive behavior, and clear project status presentation. | `.github/agents/designer.agent.md` |
| Coder | GPT-5.5 (copilot) | Implements assigned application logic and runnable-app support, follows project patterns, and validates changes. | `.github/agents/coder.agent.md` |
