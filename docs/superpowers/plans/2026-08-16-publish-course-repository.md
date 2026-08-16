# Publish Course Repository Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish the current project as the public GitHub repository `Blackoutta/ai-agent-fullstack-training` with course navigation derived from the Week 01 slide deck.

**Architecture:** Keep the repository static and documentation-only: the root README links to each week, and each week README links to its sections. Source slide decks remain local context, while course code and referenced open-source archives are committed unchanged.

**Tech Stack:** Markdown, Git, GitHub CLI (`gh`)

## Global Constraints

- Root README title must be `AI Agent 全栈工程师训练营课程代码`.
- Root README contains only the title and a linked Navigation entry per week with a one-sentence summary.
- Week summaries must be derived from the matching files under `ex_ctx/PPT`.
- `referenced_opensource_code` archives remain compressed and unchanged.
- `ex_ctx/PPT` and `.DS_Store` must not be committed.
- Preserve existing history and publish directly from `main` without a pull request.

---

### Task 1: Add repository navigation and ignore rules

**Files:**
- Modify: `.gitignore`
- Create: `README.md`
- Create: `course_code/week01/README.md`

**Interfaces:**
- Consumes: `ex_ctx/PPT/week01/1-1 开营直播.pptx` and `1-2.pdf` through `1-6.pdf`.
- Produces: Root-to-week and week-to-section Markdown navigation links.

- [ ] **Step 1: Add macOS metadata to the ignore rules**

Append `.DS_Store` to `.gitignore` while preserving the existing `ex_ctx/*` rule.

- [ ] **Step 2: Create the root navigation**

Create `README.md` with exactly this structure:

```markdown
# AI Agent 全栈工程师训练营课程代码

## Navigation

- [Week 01](./course_code/week01/)：从 Agent 全栈工程师能力与首个 Loop 出发，系统学习模型 API、Streaming、Prompt Engineering、Structured Output，并完成可治理的 LLM Gateway。
```

- [ ] **Step 3: Create the Week 01 section guide**

Create `course_code/week01/README.md` with a table linking `1-1` through `1-6`. Summaries must cover:

- `1-1`: 能力模型、课程路径、AI-native 工具链、首个 Agent Loop 及其生成、改进与调试。
- `1-2`: Chat Completions/Responses、采样参数、Token 与 Context Window、结构化输出、异常分类、重试与 Model Adapter。
- `1-3`: Streaming 延迟、SSE/事件协议、服务端与客户端消费、取消、重放、Checkpoint、Trace 与测试。
- `1-4`: System Prompt、角色和行为约束、Few-shot、任务拆解、模板、Prompt 注入防护与版本管理。
- `1-5`: JSON Schema、Pydantic、原生 Structured Output、三层校验、纠错循环、失败降级与业务协议。
- `1-6`: FastAPI LLM Gateway、自有请求协议、Provider Adapter、流式转发、模板、重试/Fallback、Token/Cost/Latency 追踪与验收。

- [ ] **Step 4: Verify navigation and ignore behavior**

Run:

```bash
test "$(sed -n '1p' README.md)" = '# AI Agent 全栈工程师训练营课程代码'
for course_dir in course_code/week01 course_code/week01/{1-1,1-2,1-3,1-4,1-5,1-6}; do test -d "$course_dir"; done
git check-ignore -q .DS_Store
git check-ignore -q ex_ctx/PPT/week01/1-2.pdf
git diff --check
```

Expected: every command exits successfully with no diff errors.

- [ ] **Step 5: Commit documentation**

```bash
git add .gitignore README.md course_code/week01/README.md
git commit -m "docs: add course navigation"
```

### Task 2: Commit course code and referenced archives

**Files:**
- Add: `course_code/week01/**` excluding files already committed in Task 1.
- Add: `referenced_opensource_code/*`

**Interfaces:**
- Consumes: Existing local course code and compressed source archives.
- Produces: The complete publishable course payload tracked by Git.

- [ ] **Step 1: Verify archive shape and GitHub size compatibility**

Run:

```bash
find referenced_opensource_code -maxdepth 1 -type f \( -name '*.zip' -o -name '*.tar.gz' \) | sort
test "$(find referenced_opensource_code -maxdepth 1 -type f ! \( -name '*.zip' -o -name '*.tar.gz' \) | wc -l | tr -d ' ')" = 0
test "$(find referenced_opensource_code -maxdepth 1 -type f -size +100M | wc -l | tr -d ' ')" = 0
```

Expected: six archive paths are listed; both tests exit successfully.

- [ ] **Step 2: Stage only the requested payload**

```bash
git add course_code referenced_opensource_code
```

- [ ] **Step 3: Audit the staged payload**

Run:

```bash
test "$(git diff --cached --name-only | rg '(^|/)\.DS_Store$|^ex_ctx/' | wc -l | tr -d ' ')" = 0
test "$(git diff --cached --name-only | rg '^referenced_opensource_code/' | wc -l | tr -d ' ')" = 6
git diff --cached --check
```

Expected: no excluded path is staged, exactly six referenced archives are staged, and the diff check passes.

- [ ] **Step 4: Commit the course payload**

```bash
git commit -m "feat: add week 1 course materials"
```

### Task 3: Create and verify the public GitHub repository

**Files:**
- No local file changes.

**Interfaces:**
- Consumes: Clean local `main` branch and authenticated `gh` session.
- Produces: Public repository `https://github.com/Blackoutta/ai-agent-fullstack-training` with `origin` configured.

- [ ] **Step 1: Recheck publication prerequisites**

Run:

```bash
gh auth status
test -z "$(git status --porcelain)"
test -z "$(git remote -v)"
```

Expected: GitHub authentication succeeds, the worktree is clean, and no remote exists.

- [ ] **Step 2: Create and push the public repository**

```bash
gh repo create ai-agent-fullstack-training --public --source=. --remote=origin --push
```

- [ ] **Step 3: Verify remote state and published content**

Run:

```bash
gh repo view Blackoutta/ai-agent-fullstack-training --json nameWithOwner,visibility,url,defaultBranchRef
test "$(git rev-parse HEAD)" = "$(git ls-remote origin refs/heads/main | cut -f1)"
gh api repos/Blackoutta/ai-agent-fullstack-training/contents/README.md --jq '.content' | base64 --decode | sed -n '1,20p'
gh api repos/Blackoutta/ai-agent-fullstack-training/contents/course_code/week01/README.md --jq '.content' | base64 --decode | sed -n '1,80p'
gh api repos/Blackoutta/ai-agent-fullstack-training/contents/referenced_opensource_code --jq 'length'
```

Expected: the repository is `PUBLIC`, local and remote `main` SHAs match, both README files render the planned content, and the archive directory contains six entries.
