# Install Course README Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Remove internal `docs/` from the published course repository and install a reusable personal Skill for maintaining course README navigation from PPT/PDF lesson materials.

**Architecture:** Repository hygiene is one Git change: ignore `docs/` and remove it from the index while retaining local files. The Skill is a self-contained personal package with `SKILL.md` and generated `agents/openai.yaml`; it relies on semantic slide reading rather than a brittle repository-specific script.

**Tech Stack:** Markdown, Git, GitHub CLI, Codex Skills

## Global Constraints

- Install the Skill at `/Users/yang/.codex/skills/maintain-course-readmes`.
- Keep the Skill to `SKILL.md` and `agents/openai.yaml`; create no scripts, assets, references, or auxiliary README.
- Keep local `docs/` files but remove `docs/` from the Git index and GitHub default branch.
- Preserve `.gitignore` rules for `ex_ctx/*` and `.DS_Store` and add `docs/`.
- Do not unpack or rewrite `referenced_opensource_code` archives.
- Push repository changes to the existing `origin/main` only after local verification.
- Do not dispatch subagents; use the observed prior publication of internal `docs/` as the RED baseline.

---

### Task 1: Ignore and unpublish internal docs

**Files:**
- Modify: `.gitignore`
- Untrack, retain locally: `docs/**`

**Interfaces:**
- Consumes: Current tracked `docs/` tree and existing `origin/main`.
- Produces: A clean repository snapshot where `docs/` is ignored and absent from GitHub's default branch.

- [ ] **Step 1: Record the RED baseline**

Run:

```bash
test -n "$(git ls-files docs)"
test "$(gh api 'repos/Blackoutta/ai-agent-fullstack-training/git/trees/main?recursive=1' --jq '[.tree[].path | select(startswith("docs/"))] | length')" -gt 0
```

Expected: both checks pass, proving internal docs are currently tracked locally and remotely.

- [ ] **Step 2: Add the ignore rule**

Use `apply_patch` to make `.gitignore` exactly:

```gitignore
ex_ctx/*
.DS_Store
docs/
```

- [ ] **Step 3: Stop tracking docs without deleting local files**

Run:

```bash
git rm -r --cached docs
test -f docs/superpowers/specs/2026-08-16-course-readme-skill-design.md
git check-ignore -q docs/superpowers/specs/2026-08-16-course-readme-skill-design.md
```

Expected: Git stages deletion of tracked docs, the local design file remains, and it is ignored.

- [ ] **Step 4: Audit and commit the repository change**

Run:

```bash
test "$(git diff --cached --name-only | rg -v '^docs/' | wc -l | tr -d ' ')" = 0
git diff --check
git add .gitignore
git diff --cached --check
git commit -m "chore: ignore generated docs"
```

Expected: the commit contains `.gitignore` plus deletions under `docs/` only.

- [ ] **Step 5: Push and verify remote removal**

Run:

```bash
git push origin main
test "$(git rev-parse HEAD)" = "$(git ls-remote origin refs/heads/main | cut -f1)"
test "$(gh api 'repos/Blackoutta/ai-agent-fullstack-training/git/trees/main?recursive=1' --jq '[.tree[].path | select(startswith("docs/"))] | length')" = 0
```

Expected: local and remote SHAs match and no `docs/` path remains in the remote default-branch tree.

### Task 2: Initialize the personal Skill

**Files:**
- Create: `/Users/yang/.codex/skills/maintain-course-readmes/SKILL.md`
- Create: `/Users/yang/.codex/skills/maintain-course-readmes/agents/openai.yaml`

**Interfaces:**
- Consumes: The approved design and `skill-creator` scaffolding utility.
- Produces: An auto-discoverable personal Skill with matching UI metadata.

- [ ] **Step 1: Confirm the Skill does not already exist**

Run:

```bash
test ! -e /Users/yang/.codex/skills/maintain-course-readmes
```

- [ ] **Step 2: Initialize the minimal Skill package**

Run:

```bash
/Users/yang/.cache/codex-runtimes/codex-primary-runtime/dependencies/python/bin/python3 \
  /Users/yang/.codex/skills/.system/skill-creator/scripts/init_skill.py \
  maintain-course-readmes \
  --path /Users/yang/.codex/skills \
  --interface 'display_name=Maintain Course READMEs' \
  --interface 'short_description=Maintain course navigation from slide decks' \
  --interface 'default_prompt=Use $maintain-course-readmes to summarize new course weeks from their PPT/PDF materials and update the repository READMEs.'
```

Expected: only `SKILL.md` and `agents/openai.yaml` are created under the Skill directory.

- [ ] **Step 3: Replace the generated Skill template**

Use `apply_patch` to replace `SKILL.md` with exactly:

```markdown
---
name: maintain-course-readmes
description: Use when maintaining course repositories that add or update weekNN course code and matching PPT/PDF lesson materials, especially when root or weekly README navigation and GitHub publication must stay synchronized.
---

# Maintain Course READMEs

## Core principle

Summarize from the complete lesson sources. Never infer missing content from filenames or code alone.

## Workflow

1. **Scope the update.** Read repository instructions and `git status`. Map real `course_code/weekNN/<lesson>` directories to `ex_ctx/PPT/weekNN/<lesson>.*`; preserve unrelated changes.
2. **Read every source.** Use the available presentation skill for PPTX and PDF skill for PDF. Extract all slide/page text and visually inspect each relevant deck. If a lesson source is missing or ambiguous, list the gap and do not invent its summary.
3. **Update navigation.** Keep the root title `AI Agent 全栈工程师训练营课程代码`, followed by `## Navigation`, sorted Week links, and one sentence per Week. In each `course_code/weekNN/README.md`, use a table with linked lesson, topic, and concise content summary. Preserve lesson-local READMEs.
4. **Keep the repository clean.** Ensure `ex_ctx/`, `docs/`, and `.DS_Store` are ignored. Do not create or commit internal planning docs. Leave `referenced_opensource_code` archives compressed and unchanged.
5. **Verify before committing.** Check Week/lesson coverage, link targets, ignore rules, `git diff --check`, staged scope, likely credentials, and files over GitHub's 100 MB limit. Stage only intended paths and use a meaningful commit message.
6. **Publish only when authorized.** For an existing remote, push only after explicit user approval. For first publication, create a public same-folder-name repository with `gh` only when explicitly requested, then verify visibility, remote SHA, README content, archives, and excluded paths.

## Quick reference

| Condition | Action |
| --- | --- |
| Missing lesson source | Stop that summary and report the missing path |
| Mixed working tree | Stage exact paths; never absorb unrelated changes |
| Archive over 100 MB | Stop before push and report the blocker |
| Archive over 50 MB | Warn; direct Git is still allowed below 100 MB |
| No publish authorization | Finish local edits and verification; do not push |

## Common mistakes

- Summarizing from code names without reading the matching deck.
- Replacing or deleting a lesson's existing README.
- Committing `ex_ctx/`, `docs/`, `.DS_Store`, extracted slides, or temporary renders.
- Pushing because a remote exists rather than because the user authorized it.
```

- [ ] **Step 4: Inspect the generated UI metadata**

Run:

```bash
sed -n '1,120p' /Users/yang/.codex/skills/maintain-course-readmes/agents/openai.yaml
```

Expected: display name, short description, and default prompt exactly match Step 2; the default prompt explicitly contains `$maintain-course-readmes`.

### Task 3: Validate and exercise the Skill

**Files:**
- Validate: `/Users/yang/.codex/skills/maintain-course-readmes/SKILL.md`
- Validate: `/Users/yang/.codex/skills/maintain-course-readmes/agents/openai.yaml`

**Interfaces:**
- Consumes: Installed Skill and current course repository.
- Produces: Structural validation plus a scenario-based compliance audit.

- [ ] **Step 1: Run the official validator**

Run:

```bash
/Users/yang/.cache/codex-runtimes/codex-primary-runtime/dependencies/python/bin/python3 \
  /Users/yang/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  /Users/yang/.codex/skills/maintain-course-readmes
```

Expected: `Skill is valid!`

- [ ] **Step 2: Check concision and template hygiene**

Run:

```bash
test "$(wc -w < /Users/yang/.codex/skills/maintain-course-readmes/SKILL.md)" -lt 500
! rg -n 'TODO|TBD|\[TODO|Example [0-9]' /Users/yang/.codex/skills/maintain-course-readmes
test "$(find /Users/yang/.codex/skills/maintain-course-readmes -type f | wc -l | tr -d ' ')" = 2
```

Expected: the Skill is under 500 words, contains no template residue, and has exactly two files.

- [ ] **Step 3: Walk through the current-repository scenario**

Verify against current state:

```bash
test "$(find course_code -maxdepth 1 -type d -name 'week[0-9][0-9]' | wc -l | tr -d ' ')" = 1
test "$(find course_code/week01 -mindepth 1 -maxdepth 1 -type d -name '1-*' | wc -l | tr -d ' ')" = 6
test "$(rg -o '\[1-[1-6]\]\(\./1-[1-6]/\)' course_code/week01/README.md | wc -l | tr -d ' ')" = 6
git check-ignore -q docs/superpowers/specs/2026-08-16-course-readme-skill-design.md
test -z "$(git status --porcelain)"
```

Expected: Week 01 and all six lessons are covered, internal docs are ignored, and no unauthorized publication work remains.
