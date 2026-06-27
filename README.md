# App-Development-skills

A collection of **six senior-level app-development skills for Claude**, packaged as a Claude Code plugin marketplace. Install the whole set with one command, or upload any single skill to the Claude app.

These skills encode opinionated, staff-level craft across the full app stack — design judgment, front-end, backend, mobile, and DevOps — using **progressive disclosure** (a focused `SKILL.md` plus deeper `references/` that load only when needed).

## Skills

| Skill | Focus |
|---|---|
| **taste** | Aesthetic & editorial judgment — distinguishing good from generic; restraint, specificity, point of view |
| **ui-ux-pro-max** | Senior UI/UX & visual design — hierarchy, design tokens, component states, accessibility |
| **frontend-engineering** | Front-end implementation — semantic HTML, modern CSS, JS/TS, React, performance |
| **dotnet-engineering** | C#/.NET backends — ASP.NET Core APIs, EF Core, architecture, testing, production |
| **devops-platform-engineering** | Staff-level DevOps — diagnostics, CI/CD, IaC, Kubernetes, AWS/Azure, reliability, cost |
| **kotlin-multiplatform** | Professional iOS + Android from shared KMP code — architecture, iOS interop (SKIE), Compose MP vs native |

They’re designed to **compose**: judgment → design → implementation → ship.

---

## Install in Claude Code (one command)

```
/plugin marketplace add <your-username>/appdevelopment-skills
/plugin install appdev-suite@appdevelopment-skills
```

`appdev-suite@appdevelopment-skills` = plugin `appdev-suite` from marketplace `appdevelopment-skills`. Manage installed plugins with `/plugin`; run `/reload-plugins` if needed.

Skills auto-load when a task matches their description. Force one explicitly with its command, e.g. `/taste`, `/dotnet-engineering`, `/kotlin-multiplatform`.

### Local install (no GitHub)

```
/plugin marketplace add /absolute/path/to/appdevelopment-skills
/plugin install appdev-suite@appdevelopment-skills
```

## Use in the Claude app (claude.ai)

The Claude app installs skills individually (no marketplace). Enable **Settings → Capabilities → Code execution** and **Skills**, then **Add → Upload** a skill: zip any folder under `plugins/appdev-suite/skills/` (e.g. `taste/`) and upload it. Repeat per skill.

## Publish to GitHub

```
cd appdevelopment-skills
git init
git add .
git commit -m "appdev-suite: six senior app-development skills"
git branch -M main
git remote add origin git@github.com:<your-username>/appdevelopment-skills.git
git push -u origin main
```

Then anyone can install with the two commands above (replacing `<your-username>`).

## Structure

```
appdevelopment-skills/
├── .claude-plugin/
│   └── marketplace.json          # marketplace catalog (name: appdevelopment-skills)
├── plugins/
│   └── appdev-suite/
│       ├── .claude-plugin/plugin.json
│       ├── skills/               # the six skills (SKILL.md + references/)
│       └── README.md
├── README.md
└── LICENSE
```

## Updating

Bump `version` in `plugin.json` and `marketplace.json`, push, then re-install
(`/plugin install appdev-suite@appdevelopment-skills`) or `/plugin marketplace update appdevelopment-skills`.

## Notes

- These skills shape *how* Claude approaches work; they direct the model’s judgment, they don’t replace it.
- Some content is intentionally version-resilient (e.g. .NET, KMP, and front-end move fast) — confirm current tool/version specifics for your stack.
- Compatible with Claude Code’s plugin system; the same skill folders also work as individual uploads in the Claude app.

## License

[MIT](./LICENSE) © Mojgan Kiani
