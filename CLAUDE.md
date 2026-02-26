# CLAUDE.md — AI Assistant Guide for test-lesson

## Project Overview

This repository is a **GitHub Skills interactive course** titled "Introduction to GitHub". It is an educational template — not a traditional software project. There is no application code, no build system, no package manager, and no test suite.

The course guides learners through five fundamental GitHub operations using GitHub Actions automation:
1. Create a branch
2. Commit a file
3. Open a pull request
4. Merge a pull request

---

## Repository Structure

```
test-lesson/
├── .github/
│   ├── workflows/          # GitHub Actions — one workflow per lesson step
│   │   ├── 0-welcome.yml
│   │   ├── 1-create-a-branch.yml
│   │   ├── 2-commit-a-file.yml
│   │   ├── 3-open-a-pull-request.yml
│   │   └── 4-merge-your-pull-request.yml
│   ├── steps/              # Markdown content injected into README at each step
│   │   ├── -step.txt       # Single integer tracking current lesson step (DO NOT EDIT MANUALLY)
│   │   ├── 0-welcome.md
│   │   ├── 1-create-a-branch.md
│   │   ├── 2-commit-a-file.md
│   │   ├── 3-open-a-pull-request.md
│   │   ├── 4-merge-your-pull-request.md
│   │   └── X-finish.md
│   └── dependabot.yml      # Monthly GitHub Actions dependency updates
├── images/                 # Screenshots used in lesson content
├── README.md               # Active lesson content (managed by workflows — DO NOT EDIT MANUALLY)
├── LICENSE                 # MIT License (GitHub, Inc.)
└── .gitignore
```

---

## How the Course Works

### Step State Machine

The current lesson step is stored as a single integer in `.github/steps/-step.txt`. The progression is:

```
0 (Welcome) → 1 (Create branch) → 2 (Commit file) → 3 (Open PR) → 4 (Merge PR) → X (Finish)
```

### Workflow Pattern

Each workflow follows this pattern:

1. **Read** current step from `.github/steps/-step.txt`
2. **Check** if the triggering event matches the expected conditions for that step
3. **Advance** by calling `skills/action-update-step@v2`, which:
   - Updates `.github/steps/-step.txt` to the next step number
   - Replaces README.md content with the next step's markdown from `.github/steps/`

### Workflow Triggers

| Workflow | Trigger Event | Condition |
|----------|--------------|-----------|
| `0-welcome.yml` | Push to `main` | Step == 0 |
| `1-create-a-branch.yml` | Branch `create` event | Step == 1, branch name == `my-first-branch` |
| `2-commit-a-file.yml` | Push to `my-first-branch` | Step == 2 |
| `3-open-a-pull-request.yml` | PR opened/reopened | Step == 3, head == `my-first-branch` |
| `4-merge-your-pull-request.yml` | Push to `main` | Step == 4 |

### Required Learner Actions

- Branch must be named exactly: `my-first-branch`
- File to commit: `PROFILE.md` (in step 2)

---

## Key Files

| File | Purpose |
|------|---------|
| `.github/steps/-step.txt` | Current step integer; managed exclusively by workflows |
| `.github/steps/<N>-<name>.md` | Lesson content for step N; safe to edit for content changes |
| `.github/workflows/<N>-<name>.yml` | Automation logic for step N; edit to change trigger/condition logic |
| `README.md` | Auto-generated from step content; **never edit directly** |

---

## Conventions

### YAML (GitHub Actions)
- 2-space indentation
- Descriptive job names (`get_current_step`, `on_create_a_branch`)
- Always include `fetch-depth: 0` in checkout steps
- Use `contents: write` permission for all step-advancing workflows
- Conditional guard pattern:
  ```yaml
  if: >-
    ${{ !github.event.repository.is_template
        && needs.get_current_step.outputs.current_step == N
        && <event-specific condition> }}
  ```

### Markdown (Lesson Content)
- Emoji in section headers (`:wave:`, `:tada:`, `:keyboard:`, `:sparkles:`)
- Numbered step-by-step activity instructions
- Links to official docs at `docs.github.com`
- Images referenced from the `images/` directory with descriptive alt text
- HTML comments (`<!-- -->`) used for template author notes

---

## Development Guidelines for AI Assistants

### What to edit
- **Lesson content**: Edit `.github/steps/<N>-<name>.md` files to update instructions, explanations, or links
- **Workflow logic**: Edit `.github/workflows/<N>-<name>.yml` to adjust trigger conditions or step transitions
- **Images**: Add/replace PNGs in `images/` if screenshots need updating

### What NOT to do
- Do not edit `README.md` directly — it is overwritten by the `skills/action-update-step@v2` action
- Do not edit `.github/steps/-step.txt` directly — it is managed by workflows
- Do not add application source code, package managers, build tools, or test frameworks
- Do not change the learner-facing branch name (`my-first-branch`) or filename (`PROFILE.md`) without updating all references across steps and workflows

### Verifying Changes
There is no local test runner. To verify workflow logic:
1. Review trigger conditions in the workflow YAML
2. Check that `from_step` / `to_step` values in `skills/action-update-step@v2` calls are correct
3. Ensure step content files in `.github/steps/` match the expected step numbers

---

## Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Default branch; course starts here |
| `my-first-branch` | Required learner branch (created during course) |
| `claude/<description>-<id>` | Convention for AI assistant feature branches |

---

## Dependencies

- **GitHub Actions**: All automation via reusable action `skills/action-update-step@v2`
- **Dependabot**: Monitors GitHub Actions for updates on a monthly schedule (`.github/dependabot.yml`)
- **No npm/pip/cargo/go dependencies**
