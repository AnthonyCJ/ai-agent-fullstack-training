# 课程 README 维护 Skill 设计

## 目标

创建个人 Skill `maintain-course-readmes`，在本仓库后续增加或更新课程周次时，依据对应课件归纳课程内容、维护两级 README，并在用户授权时提交和发布。

## 仓库清理

- 在 `.gitignore` 中加入 `docs/`，保留现有 `ex_ctx/*` 和 `.DS_Store` 规则。
- 使用 `git rm --cached` 停止跟踪 `docs/`，只从 Git 索引和 GitHub 默认分支移除，本地文件保留。
- 将清理作为独立且有意义的提交推送到现有 `origin/main`。
- 验证远程递归树不存在 `docs/`，本地 `docs/` 被正确忽略。

## Skill 位置与结构

- 安装路径：`/Users/yang/.codex/skills/maintain-course-readmes`。
- 使用 `skill-creator` 的 `init_skill.py` 初始化。
- 只保留 `SKILL.md` 和推荐的 `agents/openai.yaml`，不增加脚本、模板或辅助 README。
- Skill 名称使用 `maintain-course-readmes`，UI 名称使用“Maintain Course READMEs”。

## 触发范围

当课程仓库新增或更新 `course_code/weekNN`，且需要依据 `ex_ctx/PPT/weekNN` 新写或补充根 README、Week README，或者用户要求提交/发布相应更新时使用。

## 核心工作流

1. 读取仓库指令、Git 状态、Week/小节目录和对应 PPTX/PDF。
2. 使用适用的演示文稿与 PDF 阅读能力，完整提取文本并视觉核验课件；缺少对应课件时停止猜测并报告缺口。
3. 根 README 保持固定标题和 Navigation，每个 Week 链接到 `course_code/weekNN` 并使用一句话总结。
4. 每个 Week README 以链接表列出真实存在的小节、主题和大致内容；保留小节内已有 README。
5. 保持 `ex_ctx/`、`docs/` 和 `.DS_Store` 不被提交；`referenced_opensource_code` 压缩包不解压、不改写。
6. 验证目录覆盖、Markdown 链接、忽略规则、凭据、GitHub 100 MB 单文件限制和精确暂存范围。
7. 使用有意义的提交信息；仅在用户明确授权时推送。无远程且获准首次发布时，才用 `gh` 创建同目录名公开仓库。

## 错误边界

- 课件缺失或 Week/小节无法匹配：不生成臆测摘要，列出缺失来源。
- 工作区存在无关改动：只暂存本次 README/课程范围，重叠时向用户确认。
- 压缩包超过 GitHub 100 MB：发布前停止并报告；50 MB 建议阈值只记录警告。
- 未获得发布授权：完成本地文件和验证后停止，不创建远程、不推送。

## 验证

- 使用 `quick_validate.py` 验证 Skill frontmatter、名称和目录结构。
- 检查 `agents/openai.yaml` 的展示名称、短描述和默认 Prompt 与 `SKILL.md` 一致。
- 检查 Skill 无残留模板标记、正文保持精简，并用当前仓库场景做一次流程走查：识别已有 Week 01、判断 README 已覆盖 1-1 至 1-6、要求忽略 `docs/`，且没有发布授权时不执行额外推送。
