> AGENTS.md中的第38行（python运行的Conda环境要求）是我的个人需要，使用者请自行替换或删除。

我最近试用了oh-my-codex作为多智能体工作流。

之后额度马上到了原来的3-4x，完全不够烧了——

因此尝试精简了几个重要的Markdown文件，只保留我自己最常用的功能。

****

Minimal nano-omx package for the `ralplan -> ralph -> verifier -> architect -> cancel` workflow.

Included:
- `AGENTS.md`
- `config.toml`
- `prompts/architect.md`
- `prompts/executor.md`
- `prompts/verifier.md`
- `prompts/critic.md`
- `skills/ralph/SKILL.md`
- `skills/ralplan/SKILL.md`
- `skills/cancel/SKILL.md`

Adjust local paths in `config.toml` before use.

****

其他配置设置：

在Skills中关闭了大部分功能，只保留Cancel、Ralph、Ralph Loop等。

在设置 - MCP服务器中卸载omx_team服务器、关闭了omx_trace，只保留了omx_code_intel和omx-memory、omx_state。

****

我不确定这样的流程是否会弄坏整个omx。

但是毕竟我原来就不是在Codex cli中使用的，而是Codex app——

额度真不够烧了——还要写好玩的东西——

