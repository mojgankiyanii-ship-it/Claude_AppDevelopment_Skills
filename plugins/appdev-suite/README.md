# appdev-suite

Six senior-level app-development skills for Claude, bundled as one plugin. Each skill auto-loads when a task matches its description — no special syntax needed. You can also force one with its slash command, e.g. `/taste`, `/kotlin-multiplatform`.

| Skill | What it does | Triggers on |
|---|---|---|
| **taste** | Aesthetic + editorial judgment — good vs. generic; restraint, specificity, point of view | Any creating/evaluating where quality matters |
| **ui-ux-pro-max** | Senior UI/UX & visual design — hierarchy, design tokens, states, accessibility | Building/reviewing any interface; "make it look professional" |
| **frontend-engineering** | Front-end implementation — semantic HTML, modern CSS, JS/TS, React, performance | Writing/debugging front-end code, components, styling |
| **dotnet-engineering** | C#/.NET backends — ASP.NET Core APIs, EF Core, architecture, testing, production | Writing/designing .NET, C#, Web APIs, microservices |
| **devops-platform-engineering** | Staff-level DevOps — diagnostics, CI/CD, IaC, Kubernetes, AWS/Azure, reliability | Pipelines, infra, clusters, deployments, incidents |
| **kotlin-multiplatform** | Professional iOS + Android from shared KMP code — architecture, iOS interop, Compose MP vs native | KMP, Kotlin/Native, shared mobile features |

They **compose**: on a full product, `taste` sets the bar, `ui-ux-pro-max` designs it, `frontend-engineering` / `dotnet-engineering` / `kotlin-multiplatform` implement it, and `devops-platform-engineering` ships and operates it.

## Structure

```
appdev-suite/
├── .claude-plugin/plugin.json
└── skills/
    ├── taste/                        SKILL.md + references/
    ├── ui-ux-pro-max/                SKILL.md + references/
    ├── frontend-engineering/         SKILL.md + references/
    ├── dotnet-engineering/           SKILL.md + references/
    ├── devops-platform-engineering/  SKILL.md + references/
    └── kotlin-multiplatform/         SKILL.md + references/
```

Each skill uses **progressive disclosure**: a focused `SKILL.md` plus `references/*.md` that load only when a task goes deep in that area.

## License

MIT
