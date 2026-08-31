# 安装 `gemini-flow` Skill

请将本文件作为安装说明执行：在 Codex 的 skills 目录中创建一个名为 `gemini-flow` 的 skill，并严格按照下述目录结构与文件内容落盘。

> 目标：Codex 负责识别 UI 任务、检查代码库、确定范围、review 与 validation；真正的前端 UI 规划/实现通过 `scripts/ui-agent.ps1` 调用 Antigravity CLI，并由 Gemini 3.7 Flash 执行。
>
> 不要把下面的 `references/*.md` 或 `scripts/ui-agent.ps1` 仅仅保留在 `SKILL.md` 的代码块里；必须创建为真实文件。

---

## 1. 目标目录结构

```text
gemini-flow/
├── SKILL.md
├── references/
│   ├── ui-antigravity.md
│   └── testing.md
└── scripts/
    └── ui-agent.ps1
```

如果 skill 根目录名称由 Codex 的安装机制自动决定，也必须保证上述相对路径保持一致。

---

## 2. `SKILL.md`

创建文件：

```text
gemini-flow/SKILL.md
```

内容如下：

```md
---
name: gemini-flow
description: Delegate frontend UI planning and implementation to Gemini 3.7 Flash through Antigravity CLI. Codex chooses planning or implementation from the user's current instruction.
---

# Gemini Flow

Frontend UI only: layout, hierarchy, presentation markup, component composition, CSS/Tailwind, spacing, typography, colors, responsive design, visual states, hover/focus/animation, and loading/error/empty presentation.

Do not delegate or modify logic, APIs, state, auth, routing, backend, database, data models, or unrelated code.

Preserve existing behavior unless the user explicitly requests a behavior change.

## Intent

Codex decides the workflow from the user's current instruction.

Do not ask the user to separately specify whether Gemini may modify files, and do not require a read/write flag.

Use **Plan** when the user asks to inspect, review, analyze, redesign, propose, compare, or give a UI方案 without requesting code changes.

Use **Implement** when the user asks to modify, implement, apply, build, fix, update, refactor UI, or implement an existing proposal.

If the user asks to implement directly, do not require a separate planning turn. Implementation still begins with inspection.

Normal flow:

`inspect -> understand existing UI -> establish approach -> delegate -> review -> validate if needed`

Prefer the smallest project-consistent change over rewriting the frontend.

---

## Plan

Use when the current task is analysis or proposal only.

### Workflow

1. Inspect relevant frontend files.
2. Identify current structure, components, styles, design tokens, responsive patterns, UI states, and nearby reusable implementations.
3. Read `references/ui-antigravity.md`.
4. Delegate read-only UI analysis through `scripts/ui-agent.ps1`.
5. Review the proposal against the actual codebase.
6. Return the UI plan.
7. Stop without modifying files.

### Delegation

For planning, Gemini must treat the repository as read-only.

Codex must provide:

- user's UI goal
- relevant frontend files
- existing structure/components
- reusable styles/components
- project conventions
- constraints found by Codex

Run the dedicated UI agent script in read-only planning mode:

```powershell
powershell -File scripts/ui-agent.ps1 -Mode plan -Task "<task>" -Scope "<files>"
```

The task passed to the script should include instructions equivalent to:

> Analyze the existing frontend UI and propose an implementation-ready solution.
> This is planning only.
> Do not modify files, produce patches, or claim changes were applied.
> Reuse existing components, styles, tokens, and project conventions where possible.
> Do not change logic, APIs, state, auth, routing, backend, or database behavior.
> Keep the proposed implementation scope minimal.

Gemini should return:

- current UI observations
- relevant problems
- proposed layout/hierarchy/component composition
- styling and responsive recommendations
- important visual states
- reusable existing components/patterns
- files likely to change during implementation
- risks, ambiguities, or non-UI dependencies

### Review

Before presenting the plan, Codex must remove or correct suggestions that:

- conflict with the repository
- duplicate existing components
- unnecessarily expand scope
- require unrequested logic changes
- assume files or abstractions that do not exist

Prefer existing components, utilities, tokens, and layout patterns.

Clearly separate confirmed codebase facts from recommendations.

If the user later asks to implement, reuse the latest relevant plan without making them restate it.

---

## Implement

Use when the current task explicitly requests frontend UI code changes.

### Workflow

1. Inspect the relevant frontend files.
2. Identify current structure, reusable components, styling conventions, tokens, responsive patterns, similar implementations, and likely file scope.
3. Reuse any relevant approved/recent UI plan from the conversation.
4. If no plan exists, establish a brief UI approach from the inspection before editing.
5. Read `references/ui-antigravity.md`.
6. Delegate implementation through the dedicated UI agent script.
7. Review the resulting diff.
8. Fix only integration issues caused by the current UI change.
9. Read `references/testing.md` and run relevant validation when needed.
10. Report changes and remaining issues.

Do not redesign from scratch unless the existing plan conflicts with the codebase, requirements changed, or implementation reveals a material constraint.

### Delegation script

Use `structure` for layout/hierarchy/component composition work:

```powershell
powershell -File scripts/ui-agent.ps1 -Mode structure -Task "<task>" -Scope "<files>"
```

Use `polish` for styling/spacing/typography/colors/visual-state work:

```powershell
powershell -File scripts/ui-agent.ps1 -Mode polish -Task "<task>" -Scope "<files>"
```

`scripts/ui-agent.ps1` owns Gemini/Antigravity invocation.

**Do not replace delegation by implementing the entire UI yourself if the script fails.**

If delegation fails:

1. report the exact failure;
2. verify whether `antigravity` is available on PATH;
3. do not silently fall back to self-implementing the delegated UI;
4. keep repository changes limited to any work completed before the delegation failure;
5. ask for environment repair only when execution truly cannot continue.

Codex owns intent, inspection, scope, repository context, review, integration, and validation.

### Mode

Use `structure` for:

- layout
- hierarchy
- page structure
- component composition
- presentation markup
- responsive structure

Use `polish` for:

- CSS/Tailwind
- spacing
- typography
- colors
- borders/shadows
- visual density
- hover/focus/animation
- responsive polish
- visual states

If both apply, choose the primary type of change.

### Implementation prompt

Include:

- requested result
- established/approved UI approach
- relevant file scope
- reusable components/styles
- project conventions
- constraints found during inspection

Use instructions equivalent to:

> Implement the requested frontend UI changes in the scoped files.
> Preserve existing behavior and project conventions.
> Reuse existing components, utilities, styles, and design tokens where possible.
> Do not change logic, APIs, state, auth, routing, backend, or database code.
> Avoid new dependencies unless required.
> Keep the diff limited to the requested UI work.
> Do not perform unrelated refactors.
> Return changed files, a concise summary, and remaining issues.

### Scope

Keep editable scope as narrow as reasonably possible.

If `Scope` is provided, only modify those files.

Otherwise only modify frontend files directly related to the requested UI task.

Do not expand into unrelated directories.

---

## Review

After Gemini edits the workspace, Codex must inspect the diff.

Check for:

- unrelated file changes
- accidental logic changes
- duplicated components
- incorrect assumptions
- inconsistent styling
- responsive regressions
- UI regressions
- unnecessary dependencies
- over-engineering
- frontend type errors

Delegated output is not automatically final.

Fix only issues caused by the current UI change; do not use review as permission for unrelated cleanup.

---

## Validation

Read `references/testing.md` when validation is needed.

Check relevant items:

- layout and visual consistency
- responsive behavior
- interactions and UI states
- loading/error/empty states
- hover/focus/animation
- frontend type errors
- UI regressions

Use existing project commands when relevant.

Do not introduce a new testing system solely for this workflow.

Before completion ensure:

- requested UI is implemented
- result matches the intended approach
- existing components/styles were reused where appropriate
- diff remains frontend UI-only
- behavior is preserved unless explicitly changed
- unrelated files were not modified
- relevant checks pass when available
- remaining issues are reported

---

## Core Rules

- Codex determines Plan vs Implement from the user's instruction.
- The user does not need to authorize the skill separately.
- Plan is strictly read-only.
- Implement still starts with inspection and a UI approach.
- Gemini handles frontend UI planning/implementation through Antigravity CLI.
- Codex handles intent, repository context, scope, review, integration, and validation.
- `scripts/ui-agent.ps1` is the only supported delegation entrypoint.
- Do not bypass the delegation script when it is expected to handle the task.
- Do not silently self-implement delegated UI work after an Antigravity failure.
```

---

## 3. `references/ui-antigravity.md`

创建文件：

```text
gemini-flow/references/ui-antigravity.md
```

内容如下：

```md
# Antigravity UI Delegation

This reference defines how `gemini-flow` delegates frontend UI work to Gemini through Antigravity CLI.

## Ownership

Codex owns:

- understanding the user's intent
- inspecting the repository
- deciding Plan vs Implement
- selecting `plan`, `structure`, or `polish`
- determining the file scope
- supplying repository context
- reviewing the result
- checking the diff
- integration fixes
- validation

Antigravity/Gemini owns:

- frontend UI planning
- layout and hierarchy recommendations
- presentation markup
- component composition
- CSS/Tailwind styling
- spacing and typography
- colors and visual density
- responsive UI behavior
- loading/error/empty presentation
- hover/focus/animation and visual states
- UI implementation when the task is Implement

Do not delegate:

- business logic
- API behavior
- application state semantics
- auth
- routing behavior
- backend
- database
- data models
- unrelated refactors

## Required invocation

Always delegate through:

```powershell
powershell -File scripts/ui-agent.ps1 -Mode <plan|structure|polish> -Task "<task>" -Scope "<files>"
```

Do not invoke Gemini directly from the skill.

Do not invent a different Gemini command when the script exists.

`scripts/ui-agent.ps1` owns the actual `antigravity` CLI invocation.

## Plan mode

Use `-Mode plan`.

Plan mode is strictly read-only.

Gemini must:

- inspect only the context supplied by Codex and any repository content available to Antigravity
- propose an implementation-ready UI solution
- reuse existing components, tokens, styles, and project conventions
- identify likely files that would change
- identify responsive behavior and important visual states
- identify risks or non-UI dependencies
- not modify files
- not emit or apply patches as completed work
- not change logic, APIs, state, auth, routing, backend, database, or data models

## Structure mode

Use `-Mode structure` when the primary task involves:

- page layout
- hierarchy
- presentation markup
- component composition
- responsive structure
- rearranging existing UI regions
- adding or restructuring purely presentational components

## Polish mode

Use `-Mode polish` when the primary task involves:

- CSS/Tailwind
- spacing
- typography
- colors
- borders
- shadows
- visual density
- hover/focus states
- animation
- responsive polish
- loading/error/empty visual presentation

## Scope

When Codex knows the relevant files, pass them through `-Scope`.

Example:

```powershell
powershell -File scripts/ui-agent.ps1 `
  -Mode polish `
  -Task "Update the patrol card visual hierarchy while preserving all existing behavior." `
  -Scope "src/views/patrol/patrol-list.vue,src/views/check/my-check-list.vue"
```

If `Scope` is not provided, Gemini may only modify frontend files directly related to the requested task.

Never use delegation as permission to expand into unrelated files.

## Failure policy

If `scripts/ui-agent.ps1` fails:

1. preserve the exact command error;
2. check whether the script exists;
3. check whether `antigravity` resolves on PATH;
4. do not replace Gemini delegation by silently implementing the full UI in Codex;
5. do not claim Gemini was used if the Antigravity invocation did not run successfully.

A failed delegation is a failed delegation. Report it accurately.

## Review policy

After implementation delegation, Codex must inspect the resulting diff.

Reject or fix:

- unrelated changes
- logic changes
- route/API/state changes
- duplicate components
- invented abstractions
- inconsistent styling
- responsive regressions
- unnecessary dependencies
- over-engineering
- frontend type errors caused by the UI change

Delegated output is not automatically final.
```

---

## 4. `references/testing.md`

创建文件：

```text
gemini-flow/references/testing.md
```

内容如下：

```md
# UI Validation

Use the project's existing validation commands when relevant.

Check:

- layout and visual consistency
- responsive behavior
- interactions and UI states
- loading/error/empty states
- hover/focus/animation
- frontend type errors
- UI regressions

Do not introduce a new testing system solely for this workflow.

Fix only issues caused by the current UI change.

Before completion, ensure:

- the requested UI is implemented
- the diff remains frontend UI-only
- existing behavior is preserved unless explicitly changed
- existing components/styles/tokens are reused where appropriate
- unrelated files were not modified
- relevant checks pass when available
- remaining issues are reported
```

---

## 5. `scripts/ui-agent.ps1`

创建文件：

```text
gemini-flow/scripts/ui-agent.ps1
```

内容如下：

```powershell
param(
    [Parameter(Mandatory = $true)]
    [ValidateSet("plan", "structure", "polish")]
    [string]$Mode,

    [Parameter(Mandatory = $true)]
    [string]$Task,

    [string]$Scope = ""
)

$ErrorActionPreference = "Stop"

function Assert-CommandExists {
    param(
        [Parameter(Mandatory = $true)]
        [string]$Name
    )

    $command = Get-Command $Name -ErrorAction SilentlyContinue

    if (-not $command) {
        throw "Required CLI '$Name' was not found on PATH. gemini-flow requires Antigravity CLI and will not silently fall back to self-implementation."
    }

    return $command
}

Assert-CommandExists -Name "antigravity" | Out-Null

$modePrompt = switch ($Mode) {
    "plan" {
        @"
Planning mode only.
Analyze the existing frontend UI and propose an implementation-ready solution.
Treat the repository as read-only.
Do not modify files.
Do not produce or apply patches.
Focus on layout, hierarchy, component composition, styling, responsive behavior, visual states, reusable existing patterns, likely files to change, and risks.
"@
    }

    "structure" {
        @"
Implementation mode.
Focus on layout, hierarchy, responsive structure, component composition, and presentation markup.
You may modify only the allowed frontend UI files.
"@
    }

    "polish" {
        @"
Implementation mode.
Focus on CSS/Tailwind, spacing, typography, colors, borders, shadows, responsive polish, hover/focus/animation, visual density, and visual states.
You may modify only the allowed frontend UI files.
"@
    }
}

$scopePrompt = if ($Scope) {
    "Only inspect or modify these scoped frontend files unless additional read-only context is strictly required: $Scope"
} else {
    "Only inspect or modify frontend files directly related to the task. Do not expand into unrelated directories."
}

$writePolicy = if ($Mode -eq "plan") {
    @"
This is strictly read-only.
Do not modify files.
Do not claim changes were applied.
Return analysis and an implementation-ready UI proposal only.
"@
} else {
    @"
Implement the requested UI changes.
Keep the diff limited to the requested frontend UI work.
Return changed files, a concise summary of changes, and remaining issues.
"@
}

$prompt = @"
You are the delegated frontend UI agent for Codex.

Task:
$Task

Mode:
$Mode

$modePrompt

Scope:
$scopePrompt

Rules:
- Frontend UI only.
- Preserve existing behavior and project conventions.
- Reuse existing components, utilities, styles, and design tokens where possible.
- Do not change business logic.
- Do not change APIs.
- Do not change application state semantics.
- Do not change auth.
- Do not change routing behavior.
- Do not change backend code.
- Do not change database code.
- Do not change data models.
- Avoid new dependencies unless required.
- Do not perform unrelated refactors.
- Keep the scope minimal.

$writePolicy
"@

Write-Host "[gemini-flow] Delegating UI task through Antigravity..."
Write-Host "[gemini-flow] Mode: $Mode"

if ($Scope) {
    Write-Host "[gemini-flow] Scope: $Scope"
}

& antigravity $prompt

if ($LASTEXITCODE -ne 0) {
    throw "Antigravity exited with code $LASTEXITCODE"
}

Write-Host "[gemini-flow] Antigravity delegation completed."
```

---

## 6. 安装后检查

安装完成后，必须执行以下检查。

### 检查文件是否真实存在

确认：

```text
SKILL.md
references/ui-antigravity.md
references/testing.md
scripts/ui-agent.ps1
```

都是磁盘上的真实文件，而不是只存在于某个 Markdown 代码块中。

### 检查 Antigravity CLI

在与 Codex 相同的执行环境中运行：

```powershell
Get-Command antigravity
```

如果无法解析 `antigravity`，不要声称 Gemini 已经被调用。

### 检查脚本参数

运行：

```powershell
powershell -File scripts/ui-agent.ps1 -?
```

确认脚本至少支持：

```text
-Mode plan
-Mode structure
-Mode polish
-Task
-Scope
```

### 最小只读测试

使用一个不会修改代码的测试：

```powershell
powershell -File scripts/ui-agent.ps1 `
  -Mode plan `
  -Task "Inspect the current frontend UI and return a short read-only UI observation. Do not modify files." `
  -Scope ""
```

成功标准：

1. `antigravity` 命令真实执行；
2. 没有修改仓库文件；
3. 返回 Gemini/Antigravity 的 UI 分析结果；
4. Codex 不把自己的分析冒充成 Gemini 输出。

---

## 7. Codex 安装指令

收到本文件后，请直接执行以下任务：

1. 创建 `gemini-flow` skill。
2. 按本文件中的目录结构创建所有真实文件。
3. 不要把 `references` 和 `scripts` 内容折叠回单一 `SKILL.md`。
4. 保持 PowerShell 脚本可执行。
5. 检查 `antigravity` CLI 是否能在当前 Codex 执行环境中解析。
6. 如果 CLI 可用，执行一次 `-Mode plan` 的只读 smoke test。
7. 如果 CLI 不可用，停止并准确报告环境问题；不要修改 skill 为自实现 fallback，也不要声称 Gemini 已被使用。
8. 最后报告：
   - skill 安装路径
   - 创建的文件
   - `antigravity` 是否可用
   - smoke test 是否成功
   - 若失败，原始错误信息

不要在安装过程中修改任何业务项目代码。
