# MIGRATION.md — fresh-clone / Linux rebuild notes

Written 2026-09-21 during the Windows-to-Ubuntu migration pass. This repo is
the **per-student template**: each Trillium CS student uses "Use this
template" (or Ryan copies it) to get their own copy, then opens it in a
GitHub Codespace. It is not run directly on Ryan's machine — the only local
use is editing/testing the template itself.

## Clone

```
git clone https://github.com/ryannorris-trillium/trillium-starter.git
```

Repo is **public** (verified via unauthenticated `curl` returning HTTP 200),
so cloning needs no credentials.

## Credential note (push only)

Remote is `https://ryannorris-trillium@github.com/...` — this repo belongs to
Ryan's **school** GitHub account `ryannorris-trillium`, not his personal
`ryannorris14`. On Windows the push credential came from Windows Credential
Manager. **On the new Ubuntu machine there is no credential helper entry for
`ryannorris-trillium` yet** — before pushing to this repo (or
`trillium-starters`), the orchestrator needs either:
- `gh auth login` as `ryannorris-trillium` (sets up the git credential helper), or
- a PAT for that account stored via `git credential-store` / `gh auth login --with-token`.

No secret values are recorded anywhere in this migration pass — only the
account name and the fact that a credential is needed.

## Toolchain (as declared in this repo)

- **This repo itself** needs nothing installed to view/edit — it's just
  README + two small scripts + Python source.
- **What ships to students** (via `.devcontainer/devcontainer.json`, used by
  every Codespace built from this template):
  - Base image `mcr.microsoft.com/devcontainers/python:3.12` — Python is
    pinned to 3.12 specifically because pygame has no 3.14 wheels yet (see
    commit `a3095e3`). Do not bump the Python version without checking pygame
    wheel availability.
  - Node.js LTS via the `ghcr.io/devcontainers/features/node:1` devcontainer
    feature.
  - `postCreateCommand: pip install pygame pygbag`.
  - A second config, `.devcontainer/desktop/devcontainer.json`, adds
    `ghcr.io/devcontainers/features/desktop-lite:1` and forwards port 6080 for
    programs needing a real window (Turtle/Tkinter/Playdate Simulator).
- `pygame-starter/dev.sh` additionally shells out to `npx --yes serve` at
  runtime (Node must be present; no lockfile, npx fetches on demand).

## Verification after a fresh clone/rebuild

1. `git log --oneline -5` should show `d15b7e4` at HEAD (or later).
2. Confirm no secret files were reintroduced: `git status --short --ignored`
   should be empty (this repo has no stray build artifacts committed).
3. If rebuilding the Codespace image itself: open a Codespace from this repo,
   confirm `postCreateCommand` succeeds, then run `python3 hello.py` and open
   `index.html` via Live Preview per the root README.
4. To confirm the pygame path: `bash pygame-starter/dev.sh`, wait for "built",
   check port 3000.
5. `git ls-files --eol` shows every tracked file as `i/lf` (git blobs are
   LF-normalized); this held true as of this migration pass — re-check after
   any large edit from a Windows client with `core.autocrlf` misconfigured.

## Known state / notes for the orchestrator

- Working tree was clean and `main` was up to date with `origin/main` at scan
  time (`d15b7e4909a8ea2f1c897e3e2b98b8e876cc2d8b`) — nothing needed
  committing during this pass.
- No CLAUDE.md or Claude agent-memory directory exists in this repo. Related
  context (which starters exist, how `get.sh` pulls them, the paused
  `playdate-menu-wip` branch in the sibling repo) lives in the trillium-physics
  repo's memory files:
  `.claude/agent-memory/project_cs_demo_codespace.md` and
  `.claude/agent-memory/reference_browser_lua_limits.md`.
- Sibling repo `trillium-starters` (separate remote, separate manifest) is
  where the additional starters (`pygame`, `love`, `playdate`, `web-canvas`,
  `terminal-python`, `hunt`, `madlibs`, `showdown`) live; this repo's README
  tells students how to pull one in via `get.sh`.
