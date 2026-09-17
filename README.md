# dsh-agent-group

**状态：半成品 · Status: work in progress.** 能构建、能测试、能挂载；**还不能说话**——原因见下。

给 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（dsh）的**多智能体群聊**：多个角色在同一场对话里，各自独立人设地发言。

## 现在有什么

- **房间**：持久化的房间记录，含名称、轮次模式、成员；重启实例后仍在
- **成员**：增删查、顺序、人设字段（描述/性格/场景/开场白）
- **调度器**：轮播与自由点名两种模式、轮次上限、发言位串行认领
- **冻结重抽**：一次点击的全部维度（房间/成员/轮次/relay/世代）在点击瞬间冻结成一个不可变对象，钉不住就**拒绝**而不是猜
- **投递循环**：单飞、失败必重新入队、连续失败有预算（不会空转）
- **房间运行器**：按房间管生命周期，重抽的 relay 由转录本**解析**而非由调用方传入
- **RPC + 界面**：房间与成员的完整 CRUD，在 dsh 设置页里可直接操作

## 现在**不能**做什么（重要）

**群聊开不了口。** 宿主半没有真实的 `Speaker` 实现，因为它需要一个模型；`startGroup` 会**响亮拒绝**并说明原因，而不是假装开始。

这是唯一挡路的东西，就一个接口宽：

```ts
type SpeakerFactory = (room, transcript, takes) => Speaker
type Speaker = { produce(request, signal): Promise<{ text: string }> }
```

填入它（例如用 `ctx.agents.create(...)` + `agent.followup(...)`），并让 `apply` 调用 `GroupChatService.installSpeaker(factory)` 即可。除此之外的一切都已建好、测好，并且是针对假实现验证过的。

> 注意：`startGroup` 现在**就应该**拒绝。如果它哪天在没有模型的情况下成功了，说明有人装了假 speaker。

## 验证到什么程度

| 检查 | 结果 |
| --- | --- |
| 单元测试 | 100 项（wire / intent / scheduler / conductor / rooms / remote / prompt / speaker） |
| 类型检查 | 干净 |
| 组合与挂载 | 行进入组合树、插件树加载、浏览器 bundle 被发现并服务 |
| 浏览器实测 | 房间与成员的往返（创建 → 读回 → 删除）在真实实例上通过 |

浏览器套件 `tools/browser-verify.mjs` 会启动一个真实页面驱动真实界面，并断言渲染后的 DOM，而不只是 RPC 返回值。它需要 Chromium：`npx playwright install --with-deps chromium`。

## 构建与测试

```sh
pnpm install
pnpm run verify    # 构建 + 类型检查 + 测试 + 客户端声明门禁
```

需要 Node 24+ 与 pnpm 12+，以及 PATH 上有一个 `dsh`（或设 `DSH_INSTALL_DIR`）。平台包是 peer，从不安装。

## 设计记录

`docs/STRUCTURE.md` 是这个项目的推理总纲，比本文件详细得多：为什么这样切分、五条结构规则各自落在哪、实测过的平台行为（含源码引文）、验证状态表、以及已知缺口。**改这个仓库之前先读它。**

## License

MIT
