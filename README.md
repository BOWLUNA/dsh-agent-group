# dsh-agent-group

**状态：规划中 · Status: planned.** 本仓库用于开发 DSH 的多智能体群聊插件，目前尚无可用版本。

## 计划做什么

让 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（dsh）的多个 agent 在**同一个会话**里对话，而不是各自独立的任务线程：

- **多角色同场**：每个角色有独立的系统提示词、模型与工具集
- **发言调度**：轮流发言、@提及、或由协调者决定下一个发言者
- **共享与私有上下文**：群聊可见的共同历史 + 各角色自己的私有状态
- **人可参与**：人类作为群成员发言，并决定何时让给 agent
- **角色即 agent preset**：复用 dsh 既有的 preset 机制，而不是另造一套人格系统

## 与现有能力的关系

dsh 已有 `subagent`（父→子任务委派）与 Agent Teams（协作完成任务）。本插件针对的是**另一种形态**：平等的多角色对话本身，而非任务分解。

## License

MIT
