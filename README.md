# Premiere MCP 剪辑助手 v0.1.0

[![CI](https://github.com/sylphiette269/premiere-mcp-editor-cn/actions/workflows/ci.yml/badge.svg)](https://github.com/sylphiette269/premiere-mcp-editor-cn/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green.svg)](./LICENSE)

把 `Claude Code`、`Codex` 和 `OpenClaw` 接到 Premiere Pro 的执行层，让文档、参考视频、提示词和本地素材真正落到时间线。

> ⚠️ 版本说明：当前 `v0.1.0` 为首个公开整理版，已统一中文首页、GitHub Release 模板、Issue / PR 模板和 `OpenClaw` 接入口径。当前主线不是“全自动成片”，而是“先规划、再确认、再执行、最后人工复核”的粗剪流程。

## 这是什么

`Premiere MCP 剪辑助手` 不是一个通用聊天代理，也不是只会给建议的提示词壳子。

它更接近一个 `Premiere Pro` 宿主执行器：

- 上游接 `Claude Code`、`Codex`、`OpenClaw`
- 中间做素材扫描、编辑规划、参数整理和安全边界控制
- 下游接 `bridge` 目录、`CEP` 面板和真实时间线操作

对这个仓库来说，`Premiere` 也是“把工作软件一步步接进 AI 自动化体系”的第一个公开案例。重点不在于把所有事情都交给 AI，而在于把可执行的部分交给 AI，把仍需要判断和审美的部分清楚留给人工。

## 适合谁

如果你的实际需求更接近下面这些，这个仓库就比较对路：

- 手里已经有本地素材，想让 AI 先给一版可检查的粗剪计划
- 有 `Word` 需求文档，想把说明转成镜头顺序、节奏和装配动作
- 有参考视频，想先抽出节奏和结构，再回填到自己的素材
- 只有一句提示词，但愿意先看计划、确认后再执行
- 想把 `Premiere Pro` 接进更大的工作软件 AI 自动化体系，而不是只做一个单点演示

## 不适合谁

下面这些预期，当前版本并不适合直接承诺：

- 希望一键生成最终成片，不需要人看一遍
- 希望复杂动画、精细特效和高要求审美判断都自动完成
- 还没接通 `Premiere`、`CEP`、`bridge`，就先要求 AI 直接改时间线
- 把它当成通用聊天代理、通用搜索代理或万能工作流平台来用

## 原理

```text
剪辑说明 / 参考视频 / 提示词 + 本地素材目录
                  │
                  ▼
     scan:media / plan:edit / review:edit
                  │
                  ▼
       premiere-mcp 执行层 + 规划层
          │                    │
          │                    ├── audio-beat-mcp
          │                    └── video-research-mcp
          ▼
     Bridge 目录 + PR MCP CEP 面板
                  │
                  ▼
          Adobe Premiere Pro 时间线
```

## 三种工作入口

### 1. 文档驱动

最适合已经有 `brief`、分镜说明或 `Word` 文档的情况。

```text
Word 文档 -> convert:docx -> plan:edit / review:edit -> Premiere 执行
```

### 2. 参考视频驱动

最适合“想模仿节奏和结构，但素材是自己的”这种场景。

```text
参考视频 -> blueprint -> 本地素材回填 -> Premiere 执行
```

### 3. 提示词驱动

最适合还没有正式文档，但已经知道成片方向的时候。

```text
一句需求 -> 先出粗剪计划 -> 人确认 -> Premiere 执行
```

## 快速开始

### 1. 安装依赖

在仓库根目录执行：

```bash
npm install
npm run build
npm test
```

### 2. 安装并启动 Premiere 桥接面板

```bash
cd packages/premiere-mcp
npm run install:cep
```

然后在 Premiere Pro 中：

1. 打开一个项目
2. 打开 `Window > Extensions > PR MCP`
3. 确认桥接目录是 `C:/pr-mcp-cmd`
4. 点击 `保存桥接目录`
5. 点击 `启动桥接`
6. 点击 `测试连接`

### 3. 接入 Claude Code / Codex / OpenClaw

构建完成后，`MCP` 入口在：

```text
packages/premiere-mcp/dist/index.js
```

`Codex` 示例：

```bash
codex mcp add premiere_pro --env PREMIERE_TEMP_DIR=C:/pr-mcp-cmd -- node D:/path/to/premiere-mcp-editor-cn/packages/premiere-mcp/dist/index.js
```

`Claude Code` / `OpenClaw` 核心配置：

```text
command: node D:/path/to/premiere-mcp-editor-cn/packages/premiere-mcp/dist/index.js
env: PREMIERE_TEMP_DIR=C:/pr-mcp-cmd
```

### 4. 准备输入

使用前至少给 AI 两类输入：

1. 本地素材文件夹目录
2. 以下任意一种：
   - `Word` 文档
   - 参考视频
   - 提示词

示例：

```text
素材目录在 D:/projects/product-video/assets
请先扫描这个目录里的素材，再根据这份 Word 文档给我一版粗剪计划
```

### 5. 先规划，再执行

推荐工作顺序：

1. 扫描素材目录
2. 读取 `docx` / 参考视频 / 提示词
3. 先生成粗剪计划
4. 你确认计划
5. 再调用 Premiere MCP 真正执行
6. 人工复核结果并继续精修

## 接入时要对齐的配置

| 配置项 | 说明 | 默认值 / 建议值 |
| --- | --- | --- |
| `packages/premiere-mcp/dist/index.js` | `MCP` 服务入口 | 构建后使用 |
| `PREMIERE_TEMP_DIR` | `bridge` 目录 | `C:/pr-mcp-cmd` |
| `PREMIERE_MCP_COMMAND_FILE` | 命令文件路径覆盖 | 可选，不配置时优先走 `PREMIERE_TEMP_DIR` |
| `PR MCP` 面板 | Premiere 内桥接面板 | 必须保持启动 |
| `chrome-devtools-mcp` | 浏览器参考检索增强 | 可选 |
| 素材目录 | 本地素材扫描入口 | 必填 |
| 输入方式 | `docx / 参考视频 / 提示词` | 至少一种 |

## 主要命令

根仓：

```bash
npm run build
npm test
npm run agent:dev -- "做一个 15 秒产品视频粗剪" --asset "D:/你的素材目录"
```

`packages/premiere-mcp`：

```bash
cd packages/premiere-mcp
npm run install:cep
npm run scan:media -- --input "D:/你的素材目录" --output "docs/media.md" --json "docs/media.json"
npm run plan:edit -- --docx "D:/brief/需求.docx" --media-json "docs/media.json" --output "docs/plan.md"
npm run review:edit -- --docx "D:/brief/需求.docx" --media-json "docs/media.json" --output "docs/review.md"
```

## 仓库结构

```text
premiere-mcp-editor-cn/
├── agent/                     # 顶层工作流编排、计划、记忆、报告
├── cli/                       # 命令行入口
├── scenarios/                 # 最小闭环示例
├── packages/
│   ├── premiere-mcp/          # Premiere 执行层
│   ├── audio-beat-mcp/        # 音频节拍分析层
│   └── video-research-mcp/    # 参考视频研究与蓝图层
├── test/                      # 根仓测试入口
├── scripts/                   # 根仓脚本
├── QUICKSTART.md              # 快速开始
├── CHANGELOG.md               # 版本日志
└── .github/                   # CI、Issue、PR、Release 模板
```

## 为什么拆成三个包

- `packages/premiere-mcp`：真正负责和 `Premiere Pro` 打交道，是这套系统的执行层
- `packages/audio-beat-mcp`：单独处理节拍分析和节奏参数，不把音频逻辑塞进主包
- `packages/video-research-mcp`：单独处理参考视频研究、候选收集和 `blueprint` 聚合
- 这种拆法的重点不是“包越多越高级”，而是让规划、研究和执行边界更清楚，后面也更容易继续接入其他工作软件

## 从需求到执行的落地方式

不管你是从文档、参考视频还是一句提示词开始，最后都要落到同一条执行链：

```text
用户需求
  -> MCP 客户端
  -> premiere-mcp
  -> Bridge / CEP
  -> Premiere 时间线操作
```

在这个仓库里，最常见的输入来源有三类：

- 文档：`docx -> markdown -> 规划 -> 执行`
- 参考视频：`参考视频 -> blueprint -> 规划 -> 执行`
- 节拍数据：`音频 -> beat plan -> 工具参数 -> 执行`

这里最关键的不是“AI 会不会说”，而是“最终有没有真正落到时间线”。所以这个仓库首页才会一直强调 `bridge`、`CEP` 和人工复核。

## 桥接相关环境变量

| 环境变量 | 说明 |
| --- | --- |
| `PREMIERE_TEMP_DIR` | 推荐的桥接目录配置 |
| `PREMIERE_MCP_COMMAND_FILE` | 命令文件路径覆盖 |
| `PREMIERE_MCP_RESULT_FILE` | 结果文件路径覆盖 |

## 执行边界与人工复核

- 这个项目默认不是“上来就剪”，而是“先扫描素材、先给计划、确认后执行”
- 当前更适合粗剪、初版装配、节奏规划和素材筛选
- 不适合直接承诺无人值守最终成片
- 高层关键帧、精细运动动画、复杂特效参数仍更适合人工精修
- 只要 Bridge 目录、CEP 面板或客户端配置不一致，就可能出现“工具可见但实际不执行”
- 最终结果仍应在 Premiere Pro 中人工复核镜头顺序、节奏、字幕、转场和特效

## 更新日志

- 当前版本：[`v0.1.0`](./CHANGELOG.md)
- 详细变更记录见：[CHANGELOG.md](./CHANGELOG.md)
- GitHub Release 文案模板见：[.github/RELEASE_TEMPLATE.md](./.github/RELEASE_TEMPLATE.md)

## 相关文档

- 快速开始：[QUICKSTART.md](./QUICKSTART.md)
- 已知限制：[KNOWN_ISSUES.md](./KNOWN_ISSUES.md)
- 版本记录：[CHANGELOG.md](./CHANGELOG.md)
- 项目技能：[SKILLS.md](./SKILLS.md)
- 协作说明：[CONTRIBUTING.md](./CONTRIBUTING.md)
- 安全说明：[SECURITY.md](./SECURITY.md)

## 致谢

这个项目在早期梳理 `bridge` 和 `MCP` 接入链路时，参考过 [`Adobe_Premiere_Pro_MCP`](https://github.com/hetpatel-11/Adobe_Premiere_Pro_MCP) 中的部分思路与实现。

后续这一套工作流已经按本仓库自己的运行形态、`CEP` 桥接、拆包结构和中文发布口径重新整理。

## 免责声明 / Disclaimer

本项目仅供学习、研究、剪辑流程实验和接口调试使用。

- 本项目并非 Adobe 官方项目
- 使用本项目前，请自行确认本地软件环境、协议兼容和相关服务条款
- 真实执行结果受本地 `Premiere`、素材质量、插件环境和客户端配置影响
- 因使用本项目导致的工程损失、账号限制或其他后果，由使用者自行承担

## License

本项目采用 [MIT License](./LICENSE)。
