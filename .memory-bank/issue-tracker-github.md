# Issue Tracker: GitHub

Issues for this project live as **GitHub issues** in the repository's own issue tracker.
All operations use the `gh` CLI (or the REST API with a token where `gh` is unavailable).

## Conventions

- **Create an issue**: `gh issue create --title "..." --body "..."` (heredoc for multi-line
  bodies), or `POST /repos/<owner>/<repo>/issues` via REST.
- **Read an issue**: `gh issue view <number> --comments`.
- **List issues**: `gh issue list --state open --json number,title,body,labels`.
- **Comment**: `gh issue comment <number> --body "..."`.
- **Label / close**: `gh issue edit <number> --add-label "..."`, `gh issue close <number>`.
- Infer `owner/repo` from `git remote -v`.

## Mapping SDD artifacts to GitHub

| FeatherSpec artifact | GitHub equivalent |
| --- | --- |
| Spec (`.specs/**`) | Issue — title = spec title, body = goals + acceptance criteria |
| Acceptance criterion `AC-NNN` | Task-list item (`- [ ] AC-001: ...`) in the issue body |
| Spec status `In Progress` | Issue open, optionally labelled `in-progress` |
| Spec status `Implemented` / `done/` | Issue closed with a completion comment |
| Plan file (`.plan.md`) | **Not mirrored** — plans stay local Markdown |
| Comments during implementation | Issue comments (short, decision-relevant only) |

## When a command says "create a ticket for this spec"

`gh issue create` with the spec's title and a body containing goals and the `AC-NNN`
criteria as a task list; add the issue number to the spec's *Open points*
(e.g. `GitHub: #42`) so the link survives.

## When a command says "fetch the relevant ticket"

`gh issue view <number> --comments`; use title + body as input for `/sdd-specify`.