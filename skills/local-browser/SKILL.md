---
name: local-browser
description: Test the local dev app in a real browser using agent-browser. Use when the user asks to test, check, verify, or QA something at localhost, or says "check with browser", "test in browser", "open localhost", etc. Handles auth session persistence so login is only needed once.
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
---

<!-- vendored (locally authored — no upstream) → `skills/local-browser`. Thin project wrapper over the `agent-browser` CLI (vercel-labs/agent-browser) with persistent-auth session handling, backing the `visual-reviewer` seat. NOTE: the provisioning table listed `mattpocock/skills` as the source, but no `local-browser` skill exists anywhere in that repo's tree (verified against main, sha 9603c1c); no matching upstream skill exists, so there is no re-sync source. Kept verbatim. -->

# local-browser

Browser-test the local dev app using agent-browser with persistent auth.

## Prerequisites

- The dev server must already be running — **never start it yourself**. If it's not running, ask the user to start it.

## Workflow

### 1. Open with a project-scoped persistent session

```bash
SESSION="bg-$(basename "$PWD")"
agent-browser --session-name "$SESSION" --headed open <url>
```

- **Scope the session name to the project** (`bg-<dir>`) — each named session is a separate, isolated browser instance, so concurrent agents/projects don't stomp each other's tab/nav state. A single shared name (e.g. `bg-dev`) makes parallel runs collide.
- `--session-name` saves/restores cookies + localStorage across runs (per project; auth persists after first login)
- `--headed` shows the browser so the user can intervene (login, 2FA, etc.)
- Reuse the **same** `$SESSION` for every command in a run so they hit the same instance.

### 2. Check if auth redirect happened

```bash
agent-browser get url
```

If redirected to `/signup` or `/login`:
1. Tell the user to log in via the visible browser window
2. Wait for them to confirm
3. Navigate to the original target URL

### 3. Interact via snapshots

```bash
agent-browser snapshot -i              # interactive elements with @refs
agent-browser snapshot -i -s ".class"  # scoped to a selector
agent-browser click @e1                # click by ref
agent-browser fill @e2 "text"          # fill input
agent-browser type @e2 "text"          # type without clearing
```

Re-snapshot after every navigation or DOM change — refs are invalidated.

### 4. Inspect DOM / computed styles

```bash
cat <<'JSEOF' | agent-browser eval --stdin
(() => {
  const el = document.querySelector('.my-selector');
  return JSON.stringify({
    h: el?.getBoundingClientRect().height,
    display: getComputedStyle(el).display,
  });
})()
JSEOF

agent-browser eval "document.activeElement?.tagName"
```

Use IIFE wrapper to avoid redeclaration errors across multiple eval calls.

### 5. Clean up

```bash
agent-browser close
```

## Key rules

- **Use `snapshot -i` + `@refs` for interaction** — not screenshots
- **Always use a project-scoped `--session-name` (`bg-$(basename "$PWD")`)** so auth persists between runs *and* parallel projects stay isolated
- **Always use `--headed`** so the user can see and intervene
- **Re-snapshot after any page change** — refs expire on navigation/DOM updates
- **Use `eval --stdin` with heredoc** for complex JS — shell escaping is unreliable
