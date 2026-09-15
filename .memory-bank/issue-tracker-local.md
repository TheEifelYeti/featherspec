# Issue Tracker: Local markdown

Issues for this project live as **local markdown files** — no external tracker. Suitable
for solo work, air-gapped setups, or repos that should stay fully offline.

## Conventions

Tickets are markdown files in `.specs/backlog/` (the same folder untracked specs use),
named `NNNN-slug.md` where `NNNN` continues the spec numbering. A ticket is just a spec
without implementation status — when work starts, it moves through the normal SDD
lifecycle.

- **Create an issue**: write a new spec file under `.specs/backlog/` with the request's
  title, a short description, and (once clarified) acceptance criteria.
- **Read an issue**: open the referenced file.
- **List issues**: list `.specs/backlog/*.md` (open) and `.specs/active/*.md` (in progress).
- **Comment**: append to the file's *Open points* or *Notes* section.
- **Close**: move through `/sdd-lifecycle` like any spec (`done/` = closed).

## Mapping SDD artifacts to local files

| FeatherSpec artifact | Local equivalent |
| --- | --- |
| Ticket | Spec file in `.specs/backlog/` |
| Ticket "closed" | Spec moved to `.specs/done/` with status `Implemented` |
| Comments | *Open points* / *Notes* sections in the spec |

## When a command says "create a ticket for this spec"

The spec already **is** the ticket — note in *Open points* that tracking is local and no
external issue exists.

## When a command says "fetch the relevant ticket"

Open the named file under `.specs/` and use its content as input for `/sdd-specify`.