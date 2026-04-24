Проект теперь использует четыре разных subagent'а:

- `.claude/agents/notebooklm-study-notes.md`
- `.claude/agents/go-senior-backend-interview.md`
- `.claude/agents/go-senior-backend-interview-prep.md`
- `.claude/agents/go-career-experience-storyteller.md`

Назначение:
- `notebooklm-study-notes` — работа с выдержками из NotebookLM, учебные конспекты, Obsidian-ready markdown, строгий source-grounded synthesis;
- `go-senior-backend-interview` — помощь на технических интервью по Go/backend, theory-grounded ответы и live coding только по материалам этого репозитория;
- `go-senior-backend-interview-prep` — подготовка к senior Go/backend интервью и system design: gap analysis, study plans, mock interviews, answer review и drills только по материалам этого репозитория;
- `go-career-experience-storyteller` — подготовка подробных и правдоподобных рассказов по карьерному опыту на основе `Resume/Senior Golang Developer.md`, с разделением подтверждённых фактов и допустимой реконструкции, плюс follow-up подготовка.

Общее:
- для автоматического доступа к NotebookLM используется project-scoped MCP из `.mcp.json`;
- выбор целевого notebook настраивается в `.claude/notebooklm-config.toml`.
