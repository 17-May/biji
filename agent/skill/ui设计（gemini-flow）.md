---
name: gemini-flow
description: Delegate frontend UI work to Gemini 3.7 Flash.
---

# Gemini Flow

Frontend UI only: layout, presentation markup, CSS/Tailwind, responsive design, visual states.

## Workflow

1. Inspect relevant frontend files.
2. Read `references/ui-antigravity.md`.
3. Delegate with Gemini 3.7 Flash.
4. Review diff and integration.
5. Read `references/testing.md` when validation is needed.

Do not delegate logic, API, state, auth, routing, backend, or database work.

## references/ui-antigravity.md

```md
name: gemini-flow
description: Delegate frontend UI work through scripts/ui-agent.ps1.
---

# Gemini Flow

Frontend UI only: layout, presentation markup, CSS/Tailwind, responsive design, visual states.

## Workflow

1. Inspect relevant frontend files.
2. Read `references/ui-antigravity.md`.
3. Run:
   `powershell -File scripts/ui-agent.ps1 -Mode <structure|polish> -Task "<task>" -Scope "<files>"`
4. Review the resulting diff and fix integration issues.
5. Read `references/testing.md` when validation is needed.

`scripts/ui-agent.ps1` owns Gemini/Antigravity invocation.
Do not replace delegation by implementing the UI yourself if the script fails.

Do not delegate logic, API, state, auth, routing, backend, or database work.
```

## references/testing.md

```md
# UI Validation

Check:

- layout and visual consistency
- responsive behavior
- interactions and UI states
- loading/error/empty states
- hover/focus/animation
- frontend type errors
- UI regressions

Use existing project commands when relevant.

Fix only issues caused by the current UI change.

Before completion, ensure the requested UI is implemented, the diff remains UI-only, relevant checks pass, and remaining issues are reported.
```

## scripts/ui-agent.ps1

```powershell
param(
    [Parameter(Mandatory = $true)]
    [ValidateSet("structure", "polish")]
    [string]$Mode,

    [Parameter(Mandatory = $true)]
    [string]$Task,

    [string]$Scope = ""
)

$ErrorActionPreference = "Stop"

$modePrompt = if ($Mode -eq "structure") {
    "Focus on layout, hierarchy, responsive structure, and presentation markup."
} else {
    "Focus on CSS/Tailwind, spacing, typography, colors, responsive polish, and visual states."
}

$scopePrompt = if ($Scope) {
    "Only modify: $Scope"
} else {
    "Only modify frontend files directly related to the task."
}

$prompt = @"
Task:
$Task

$modePrompt
$scopePrompt

Frontend UI only. Preserve behavior and project conventions.
Do not change logic, APIs, state, auth, routing, backend, or database.
Avoid new dependencies unless required.

Return changed files, changes, and issues.
"@

antigravity $prompt

if ($LASTEXITCODE -ne 0) {
    throw "Antigravity exited with code $LASTEXITCODE"
}
```
