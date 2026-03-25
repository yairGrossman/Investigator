# Investigator

A Claude Code subagent that traces data flows through codebases and writes clear, structured documentation into an `Investigators/` folder.

---

## What It Does

The Investigator reads your source code — across any number of files and tech layers — and produces a single Markdown file documenting exactly how data moves through the system.

Two modes:

| Mode | When to use |
|------|------------|
| **Data Flow Trace** | You have existing code and want to understand how data travels from point A to point B |
| **Feature Spec** | You want to implement something new and need to know which files across all layers need to be created or modified |

---

## Install

### Project-level (available only in this repo)
The agent and command are already in `.claude/` — Claude Code picks them up automatically.

### User-level (available in all your projects)

**macOS / Linux:**
```bash
cp .claude/agents/investigator.md ~/.claude/agents/investigator.md
cp .claude/commands/investigate.md ~/.claude/commands/investigate.md
```

**Windows:**
```powershell
Copy-Item .claude\agents\investigator.md "$env:USERPROFILE\.claude\agents\investigator.md"
Copy-Item .claude\commands\investigate.md "$env:USERPROFILE\.claude\commands\investigate.md"
```

---

## Usage

Two ways to invoke — they do the same thing:

| Style | Example |
|-------|---------|
| `/investigate` (slash command) | `/investigate how does the counter flow from the UI to the server?` |
| `@investigator` (direct agent) | `@investigator how does the counter flow from the UI to the server?` |

You can specify a `from` point, a `to` point, both, or neither.

### Trace a full data flow
```
@investigator trace the button click flow from onClick in SendButton.tsx to the UDP send
```

### Trace from a starting point to the natural end
```
@investigator from the login form submit handler
```

### Trace from the entry point to a specific layer
```
@investigator to the C++ addon layer — what calls get made when the user presses record?
```

### Trace a complete flow (no bounds)
```
@investigator how does the counter get from the UI to the server?
```

### Plan a new feature (spec mode)
```
@investigator implement feature: dark mode toggle across all layers
```
```
@investigator what files do I need to add a real-time notification badge to the UI?
```

---

## Output

The agent creates `Investigators/[topic].md` in your current working directory.

### Data Flow Trace output
```markdown
# Investigation: Button Click → UDP Send

**Date:** 2026-03-25
**From:** onClick in SendButton.tsx
**To:** UDP send in network.cpp

---

## Data Flow Summary

`onClick (SendButton.tsx:42)` → `useCounter (hooks.ts:10)` → `ipcRenderer.send (hooks.ts:31)` → `ipcMain.handle (main.ts:88)` → `sendUDP (network.cpp:204)`

---

## Detailed Flow

### 1. onClick — SendButton.tsx:42
**Signature:** `onClick(event: React.MouseEvent<HTMLButtonElement>): void`
**Purpose:** Fires when the user clicks the Send button. Reads the current counter value
from component state and passes it to `useCounter` via the `onSend` callback prop.
No data transformation happens here — it is purely a UI trigger.

### 2. useCounter — hooks.ts:10
...
```

### Feature Spec output
```markdown
# Feature Spec: Dark Mode Toggle

**Date:** 2026-03-25

---

## Intended Data Flow (once implemented)

`ThemeToggle (UILayer)` → `useTheme (HooksLayer)` → `ipcRenderer.send (IPCRenderer)` → `ipcMain.handle (IPCMain)` → `nativeWindow.setTheme (NativeAddon)`

---

## Files to Create or Modify

### UI Layer
- **src/components/ThemeToggle.tsx** *(create)* — Toggle button; calls `useTheme().toggle()`
- **src/App.tsx** *(modify)* — Mount ThemeToggle in the header

### Hooks Layer
- **src/hooks/useTheme.ts** *(create)* — Manages theme state in localStorage; dispatches IPC call on change
...
```

---

## Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `from` | Starting point: function name, `file:line`, component, or description | Entry point of the flow |
| `to` | Stopping point: layer name, file, function, or description | Natural end of the flow |
| *(neither)* | Full trace from entry to end | — |

---

## Supported Tech Stacks

The agent knows how to trace across:
- React / Next.js components and hooks
- Electron IPC (renderer → preload → main)
- Node.js servers (Express, Fastify, WebSocket)
- Native addons (N-API / node-addon-api, C/C++)
- Network protocols (UDP, TCP, HTTP, WebSocket)
- Event emitters, Redux, Zustand, Context API
- Any combination of the above
