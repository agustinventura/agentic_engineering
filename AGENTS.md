# Agentic Engineering Template

This is an example of Agentic Engineering workflow

## SDLC Commands

### 🏗️ 1. Build
Place here your application build workflow and commands

### 🧪 2. Test
Place here your application test workflow and commands

### 🚀 3. Local Run
Place here your application local run workflow and commands

## 📖 Progressive Disclosure (Documentation)

References here your application relevant information, think of this section as a documentation index, so the agents can load only needed information.

*Golden Rule*: **Don't read all documentation at once**. Use this as an index and dig deeper in the specific files only if your actual task requires it.

## Critical rules
Place here rules you always want your agents to follow, for example, never commit.

### 🤖 Multi-Agent Architecture
This project uses a specialized agent system with distinct roles to prevent cognitive overload. Read their profiles on demand before delegating tasks:

* **Manager Agent (Orchestrator)**: `.github/agents/manager.md` - Administrative runner and human-facing orchestrator. Controls the workflow, creates task folders, enforces Human-in-the-Loop (HITL) approvals by presenting draft files, and dynamically routes events via `messages.jsonl`. Does not read or write application code.
* **Leader Agent (Planner/Architect)**: `.github/agents/leader.md` - Technical lead. Runs in the background to handle Task Triage and Technical Planning, outputting `draft_` files for the Manager. Extracts learnings to update `MEMORY.yaml`.
* **Scout Agent**: `.github/agents/scout.md` - Codebase explorer and context filter.
* **Implementer Agent**: `.github/agents/implementer.md` - Executes the implementation plan and writes the code.
* **Reviewer Agent**: `.github/agents/reviewer.md` - Audits the implementation against the plan.
* **Documenter Agent**: `.github/agents/documenter.md` - Updates documentation based on the completed cycle.

### 🔄 Communication Protocol and State Management

The golden rule is to avoid the "broken telephone" effect: agents **NEVER** pass code or extensive context directly through the chat. All coordination happens on disk inside the `agents_workspace/` directory, using structured data formats to prevent parsing errors and ambiguity.

#### Long-Term Memory (Persistent)
* `agents_workspace/MEMORY.yaml`: Acts as the project's episodic and semantic memory. Its sole function is to store persistent architectural decisions, project conventions, and lessons learned.
* **Read Rule**: The Leader Agent must read this file before drafting a strategy or action plan.
* **Update Rule**: After a successful task cycle, the Leader must synthesize an atomic summary with the new rules or discovered patterns and append it to this file.

#### Event Bus (Communication Log)
* `agents_workspace/messages.jsonl`: Serves as the central notification system. The Manager Agent appends JSON objects tracking the timestamp, origin, destination, action, and the specific `.yaml` file reference.

#### Short-Term Memory & The "Draft & Promote" Pattern
Task execution files are strictly isolated in subdirectories per task (e.g., `agents_workspace/tasks/t1_endpoint_setup/`).

To allow human supervision without breaking background processes, the system uses a **Draft & Promote** pattern:
1. The Leader writes `draft_task_strategy.yaml` or `draft_implementation_plan.yaml`.
2. The Manager pauses, reads the draft, and asks the human for approval in the chat.
3. Upon approval, the Manager renames the file (removing the `draft_` prefix) and continues execution.

**Standard File Templates (`agents_workspace/templates/`):**
* `task_strategy.yaml`: Defines the dynamic execution workflow (which agents to invoke and in what order).
* `scout_mission.yaml`: Details the precise instructions and target files the Scout must investigate.
* `scout_findings.yaml`: Written by the Scout depositing the pre-filtered context.
* `implementation_plan.yaml`: Contains the definitive action plan and strict acceptance criteria.
* `implementation_result.yaml`: Saved by the Implementer containing the list of modified files and execution notes.
* `reviewer_result.yaml`: Written by the Reviewer issuing an approved or rejected verdict.
* `documenters_result.yaml`: Logged by the Documenter registering updates to manuals and diagrams.