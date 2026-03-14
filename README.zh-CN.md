# Premiere Agent

[English README](./README.md)

`Premiere Agent` 是一个面向 `Claude Code` 和 `Codex` 的 Windows 优先
monorepo，目标是通过 MCP 工具驱动 Adobe Premiere Pro，完成**视频粗剪**
阶段的辅助制作。

它不是“自动成片神器”，而是把文档需求、参考视频、提示词和本地素材目录
收口到一条可追踪的 Premiere 粗剪工作流里。

## 这个项目的作用

- 让 `Claude Code` 或 `Codex` 通过 MCP 工具读取和操控 Premiere Pro
- 根据 Word 文档、参考视频或提示词，辅助完成视频粗剪
- 结合本地素材目录做素材扫描、装配规划、节拍辅助和时间线执行
- 输出可检查的 plan、checkpoint 和报告，方便人工复核

## 支持的输入方式

你可以用这三类方式驱动它：

- 提供一个写明视频需求或剪辑步骤的 Word 文档
- 提供一个参考视频，让系统先分析风格再生成蓝图
- 直接给提示词，让 AI 先做粗剪规划

无论走哪条路径，**都要把素材文件夹目录给 AI**。  
如果没有素材目录，AI 不知道本地有哪些素材可用，也无法安全规划和导入。

## 使用方式

1. 把这个仓库对应的 MCP 服务接到 `Claude Code` 或 `Codex`
2. 先告诉 AI 本地素材文件夹路径
3. 再补充下面任意一种输入：
   - `.docx` 需求文档
   - 参考视频路径
   - 直接提示词
4. 让 AI 先扫描素材、生成计划，再调用 Premiere MCP 做粗剪
5. 最后在 Premiere Pro 里人工检查并继续精修

典型命令示例：

```bash
npm run agent:dev -- "做一个 15 秒产品视频粗剪" --asset "D:/你的素材目录"
npm run agent:dev -- "按 Word 文档做粗剪" --docx "D:/brief/需求.docx" --manifest "D:/你的素材目录/media.json"
npm run agent:dev -- "参考这个视频的节奏做粗剪" --editing-blueprint "D:/research/blueprint.json" --asset "D:/你的素材目录"
```

## 环境要求

- Windows
- Node.js 18+
- Adobe Premiere Pro
- 已启用 CEP
- 已安装 `packages/premiere-mcp` 提供的 CEP 面板

推荐初始化流程：

```bash
npm install
npm run build
npm test
npm run agent:dev -- "做一个 15 秒产品视频粗剪" --asset "D:/你的素材目录"
```

## 当前未完全实现 / 不稳定的部分

这个项目目前更适合“粗剪辅助”，不适合直接当作稳定的全自动精剪系统。

- 关键帧动画调整还不够稳定，尤其是精细动画和重复修正场景
- 转场插入仍依赖 Premiere 宿主行为和 QE DOM，不是所有转场都稳定
- 特效应用和特效参数写入在不同工程状态下仍可能失败或漂移
- 最终结果仍然需要在 Premiere Pro 里人工复核和继续调整

如果你的目标是成片交付，当前应该把它理解为：
**AI 帮你完成规划和粗剪，人再完成精修。**

## 仓库结构

```text
repo-root/
├── agent/                  # 编排、大脑、记忆、critic、reporter
├── cli/                    # 命令行入口
├── scenarios/              # 最小闭环示例
├── packages/
│   ├── premiere-mcp/       # Premiere 执行层
│   ├── audio-beat-mcp/     # 节拍分析层
│   └── video-research-mcp/ # 研究与蓝图层
└── .github/workflows/ci.yml
```

## 当前仓库状态

- 根仓 build / test / CI 已打通
- 已拆成 `premiere-mcp`、`audio-beat-mcp`、`video-research-mcp` 三层
- 已有顶层 agent 编排入口
- 当前主目标仍然是把“可检查的粗剪闭环”做稳，而不是做一键成片

## 协作与安全

- 贡献说明见 [CONTRIBUTING.md](./CONTRIBUTING.md)
- 安全问题见 [SECURITY.md](./SECURITY.md)
