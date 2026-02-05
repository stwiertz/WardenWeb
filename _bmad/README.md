# BMAD Method - WardenWeb

This project uses the **BMAD Method** (Breakthrough Method for Agile AI-Driven Development) v6.0.0-Beta.7.

## Directory Structure

```
_bmad/
├── agents/           # AI agent definitions
├── workflows/        # Development workflows
├── data/            # Shared data and templates
├── teams/           # Team configurations
├── tasks/           # Task definitions
├── checklists/      # Quality checklists
├── agent-components/ # Reusable agent components
├── planning-artifacts/       # Phase 1-3 artifacts (briefs, PRDs, architecture)
├── implementation-artifacts/ # Phase 4 artifacts (sprints, reviews)
└── bmad-config.yaml # Configuration file
```

## Available Agents

- **bmad-master** - Master orchestrator agent
- **analyst** - Business/requirements analyst
- **architect** - System architect
- **dev** - Developer agent
- **pm** - Product manager
- **qa** - Quality assurance
- **sm** - Scrum master
- **ux-designer** - UX/UI designer
- **tech-writer** - Technical documentation writer
- **quick-flow-solo-dev** - Solo developer quick flow

## Key Workflows

### Analysis Phase (1)
- `create-product-brief/` - Create product briefs
- `research/` - Market, domain, and technical research

### Planning Phase (2)
- `create-prd/` - Product Requirements Document
- `create-ux-design/` - UX design workflows

### Implementation Phase (4)
- `sprint-planning/` - Sprint planning
- `sprint-status/` - Sprint tracking
- `code-review/` - Code review process

### Quick Flows
- `bmad-quick-flow/` - Rapid development workflow

## Documentation

- [BMAD Method Docs](http://docs.bmad-method.org/)
- [GitHub Repository](https://github.com/bmad-code-org/BMAD-METHOD/)

## Usage

To work with BMAD agents, reference the agent files in `_bmad/agents/` and use the workflows in `_bmad/workflows/`.
