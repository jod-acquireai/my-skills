# my-skills

Claude Code skills, installable with `npx`.

## Skills

| Skill | Slash command | What it does |
|---|---|---|
| [servicedesk-to-github](skills/servicedesk-to-github/SKILL.md) | `/servicedesk-to-github` | Reads your pending requests from the ServiceDesk "My View" (`servicedesk.intellihub.acquire.ai`) in Chrome and opens one GitHub issue per request, skipping ones already mirrored. |

## Install

Install for your user (available in every project):

```bash
npx skills add jod-acquireai/my-skills --skill servicedesk-to-github -g -a claude-code
```

Or for the current project only (writes to `./.claude/skills/`):

```bash
npx skills add jod-acquireai/my-skills --skill servicedesk-to-github -a claude-code
```

Restart Claude Code after installing. A skill in `~/.claude/skills/` is both a skill Claude can pick up from context and a slash command, so `/servicedesk-to-github` works with no extra setup.

Update later with `npx skills update`, or remove with `npx skills remove servicedesk-to-github`.

## servicedesk-to-github

### Requirements

- [Claude in Chrome](https://claude.com/chrome) extension installed and connected (`claude --chrome`, or `/chrome` inside a session).
- You are signed in to the ServiceDesk in that Chrome profile. If you are not, the skill stops and asks you to sign in; it never types credentials.
- GitHub CLI signed in: `gh auth login`.

### Usage

From inside the target repo:

```text
/servicedesk-to-github
```

Options:

```text
/servicedesk-to-github --repo my-org/my-repo   # target a different repo
/servicedesk-to-github --dry-run               # preview, create nothing
/servicedesk-to-github --yes                   # skip the confirmation step
/servicedesk-to-github --label support         # use a different label (default: servicedesk)
```

You can also just ask: "turn my pending ServiceDesk requests into GitHub issues".

### How it works

1. Lists existing issues with the `servicedesk` label and reads the hidden `<!-- servicedesk-request-id: N -->` marker in each, so a request is never mirrored twice.
2. Opens the ServiceDesk "My View" in a new Chrome tab and collects requests that are not Resolved, Closed, Completed, Cancelled or Rejected.
3. Shows a create/skip table and asks you to confirm (unless `--yes`).
4. Creates issues titled `[SD-<id>] <subject>` with status, priority, requester, assignee, dates, a link back to the request and the description.

It is read-only on the ServiceDesk. If the target repo is public, it warns you and leaves out descriptions and requester names unless you say otherwise.
