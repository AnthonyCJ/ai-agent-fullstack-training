# AI Agent 训练营课程仓库发布设计

## 目标

将当前目录发布为 GitHub 公开仓库 `Blackoutta/ai-agent-fullstack-training`，让读者能从根 README 快速导航到每周课程代码，并在每周 README 中了解各小节内容。

## 内容结构

- 根 `README.md` 的标题固定为“AI Agent 全栈工程师训练营课程代码”。
- 根 README 只提供 Navigation 目录；每个条目链接到 `course_code` 下对应的 Week，并用一句话概括该周主题。
- 每个 `course_code/weekNN/README.md` 按小节列出链接、主题和简要内容。
- 当前只有 `week01`，其 README 覆盖 `1-1` 至 `1-6`。
- 已存在的 `course_code/week01/1-6/README.md` 保留，不覆盖。

## 摘要依据

README 摘要只依据 `ex_ctx/PPT/week01` 的对应课件：

- `1-1 开营直播.pptx`：课程能力模型、学习路径、首个 Agent Loop 与 AI 辅助开发。
- `1-2.pdf`：模型 API、采样参数、Token、结构化输出、异常处理与 Model Adapter。
- `1-3.pdf`：Streaming、SSE、事件协议、取消、恢复、Checkpoint 与可观测性。
- `1-4.pdf`：System Prompt、行为约束、Few-shot、任务拆解、模板、注入防护与版本管理。
- `1-5.pdf`：JSON Schema、Pydantic、原生 Structured Output、校验、纠错和降级。
- `1-6.pdf`：FastAPI LLM Gateway、Provider Adapter、流式转发、模板、重试、Fallback 和调用追踪。

## 发布范围

- 提交 `course_code` 全部内容。
- `referenced_opensource_code` 中的压缩包保持原样直接提交，不解压、不修改。
- `ex_ctx/PPT` 仅作为摘要来源，继续由 `.gitignore` 排除，不上传。
- 在 `.gitignore` 中加入 `.DS_Store`，并确保现有 `.DS_Store` 不进入提交。
- 保留现有 Git 历史，在 `main` 分支提交并直接推送；不创建 PR。

## 验证与发布

发布前检查 README 链接指向真实目录、每个 Week 和小节均被覆盖、课件与 `.DS_Store` 未被跟踪、压缩包均被跟踪且单文件小于 GitHub 100 MB 限制。然后使用 `gh repo create ai-agent-fullstack-training --public --source=. --remote=origin --push` 创建并推送仓库，最后通过 `gh repo view` 和远程文件读取验证仓库可公开访问且内容已上传。
