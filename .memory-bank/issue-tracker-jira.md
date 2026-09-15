# Issue Tracker: Jira

Issues for this project are tracked in **Jira** (Atlassian Cloud). All operations use the
Jira REST API v3 with HTTP Basic Auth (`JIRA_EMAIL` + `JIRA_API_TOKEN` from the environment;
`JIRA_URL` is the instance base URL, e.g. `https://yourcompany.atlassian.net`).

## Conventions

- **Create an issue**: `POST $JIRA_URL/rest/api/3/issue` with
  `{"fields": {"project": {"key": "<PROJECT-KEY>"}, "summary": "...", "description": {ADF doc}, "issuetype": {"name": "Story"}}}`.
  Descriptions in Jira Cloud v3 use **Atlassian Document Format (ADF)** — wrap text as
  `{"type": "doc", "version": 1, "content": [{"type": "paragraph", "content": [{"type": "text", "text": "..."}]}]}`.
- **Read an issue**: `GET $JIRA_URL/rest/api/3/issue/<KEY>?fields=summary,status,issuetype,priority,assignee,labels,description`
  — always pass `fields=`, otherwise Jira returns every custom field, comment and attachment
  (easily 10–50× more data).
- **List issues (JQL)**: enhanced search `GET $JIRA_URL/rest/api/3/search/jql?jql=<urlencoded>&maxResults=20&fields=summary,status`
  — the old `/search` endpoints are deprecated; pagination is cursor-based (`nextPageToken`).
- **Comment**: `POST $JIRA_URL/rest/api/3/issue/<KEY>/comment` with an ADF body.
- **Transition (status change)**: first `GET .../issue/<KEY>/transitions` to list available
  IDs, then `POST .../issue/<KEY>/transitions` with `{"transition": {"id": "<ID>"}}`.
  Never hardcode status names — they may be localized (e.g. German instances).
- **Link issues**: `POST $JIRA_URL/rest/api/3/issueLink` with
  `{"type": {"name": "Blocks"}, "inwardIssue": {"key": "..."}, "outwardIssue": {"key": "..."}}`.

## Project settings (filled in by /sdd-setup)

- **Project key**: `TBD` — the Jira project this repo's issues live in.
- **Issue types**: default `Story` for feature specs, `Bug` for defects, `Task` for chores.
- **Status mapping**: a spec moving to `.specs/active/` corresponds to the Jira issue being
  transitioned to the project's "In Progress" equivalent; `.specs/done/` corresponds to
  the project's "Done" equivalent. Resolve the actual transition IDs from the issue's
  `/transitions` response at runtime (names may be localized).

## When a command says "create a ticket for this spec"

Create a Jira issue in the configured project with the spec's title as summary, the
acceptance criteria as ADF description, and put the returned issue key into the spec's
*Open points* or header (e.g. `Jira: PROJ-123`) so the link survives.

## When a command says "fetch the relevant ticket"

Read the issue via `GET /issue/<KEY>?fields=summary,description,status,labels` and use
summary + description as input for `/sdd-specify`.

## Mapping SDD artifacts to Jira

| FeatherSpec artifact | Jira equivalent |
| --- | --- |
| Spec (`.specs/**`) | Issue (Story/Bug) — summary = spec title, ADF description = goals + acceptance criteria |
| Acceptance criterion `AC-NNN` | Checklist item in the issue description |
| Spec status `In Progress` | Issue status in the "In Progress" category (`statusCategory = "In Progress"`) |
| Spec status `Implemented` / `done/` | Issue transitioned to the "Done" category (`statusCategory = "Done"`) |
| Plan file (`.plan.md`) | **Not mirrored** — plans stay local Markdown (Jira issues should not carry implementation detail) |
| Comments during implementation | Jira issue comments (short, decision-relevant only) |

## Pitfalls

- **Auth**: HTTP Basic `email:api_token` — never a password. Tokens:
  https://id.atlassian.com/manage-profile/security/api-tokens
- **ADF, not plain text**: v3 API rejects plain-string descriptions.
- **`fields=` always**: full payloads are 50–200KB per issue without it.
- **Localized statuses**: query `/transitions` and match by ID; `statusCategory`
  (`Done`/`In Progress`/`To Do`) is locale-independent and safe for JQL.
- **Labels PUT replaces**: fetch current labels first when adding one.
- **Rate limits**: ~40 search requests per 10s — add small delays in bulk loops.
- **Missing credentials**: if `JIRA_URL`/`JIRA_EMAIL`/`JIRA_API_TOKEN` are unset, say so and
  fall back to local markdown tickets under `.specs/backlog/` until the user configures them.