# Quiet Axis

**给任何 AI 编程 Agent 用的可移植设计技能。** 它把"做得好看"拆成 Agent 能执行、能验收的顺序：分类内容 → 划分页面 → 建立层级 → 用动效解释状态变化。

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [Español](README.es.md)

> **作者：[@dream120526-create](https://github.com/dream120526-create) 与 [@appledream120526-cloud](https://github.com/appledream120526-cloud)（共同作者）。** 若在其他地方看到本项目，原始仓库为 [dream120526-create/quiet-axis](https://github.com/dream120526-create/quiet-axis) 与 [appledream120526-cloud/quiet-axis](https://github.com/appledream120526-cloud/quiet-axis)。

---

## 为什么做这个

Agent 生成的界面通常死在三处：内容没分类、页面没分区、所有元素一样重。Quiet Axis 是一份 Markdown 技能，给 Agent 少量硬规则、一份设计契约和一套看证据的验收门槛，同时不绑定框架，也不绑定某个厂商。

它刻意做到**小到能常驻上下文**，又**具体到能改变输出**。

## 包内有什么

| 文件 | 作用 |
| --- | --- |
| `SKILL.md` | 入口：改动分级、七步流程、默认值、四条动效曲线 + 弹簧分支、25 项 P0 |
| `DESIGN.md` | 可填写的设计契约：产品事实、一句话北极星、每屏主角、令牌、Do/Don't |
| `CHEATSHEET.md` | 一页速查卡，10 分钟够用 |
| `topics/motion.md` | 6 个按钮交互 + 16 个 UI 动效，含可运行代码与 SwiftUI/Compose 等价实现 |
| `topics/spring.md` | 弹簧分支：滑关、橡皮筋、吸附、连续圆角、液态玻璃策略 |
| `topics/frameworks.md` | 可直接运行：CSS、Tailwind、Framer Motion、GSAP、Lottie、SwiftUI、Compose、Tauri/Electron |
| `topics/patterns.md` | 10 种页面形态、数据密集页，以及"第 11 种怎么推导" |
| `topics/recipes.md` | 令牌、外壳、按钮、对话框、Toast、骨架屏、列表/设置/表单整页 |
| `topics/reference.md` | 48 条反模式、实测令牌对比度、排版、无障碍、评审格式 |
| `topics/i18n.md` | 中英混排、标点、数字、RTL、字体栈 |
| `topics/delivery.md` | HTML / IPA / APK / EXE 构建、签名、真机验收 |
| `references/agent-ui-skills.md` | 14 个 Agent UI 技能的方法论对照 |

几个关键点：

- **不是形态大全。** 只有 10 个骨架，但给了推导第 11 个的方法：列清单、问 2–3 个形态问题、套五区域、写进 `DESIGN.md`。
- **AI 味审计。** 18 条信号（紫蓝渐变、四张同款 KPI 卡、emoji 图标、假指标），按 Audit → Direction → Passes 修。
- **动效符合意图。** 匀速用 `linear`；离开用 `ease-in`；进入用 `ease-out`；页内位移用 `ease-in-out`；回弹只用于反馈。
- **要证据，不听说辞。** 真实 hex 对比度、390/768/1440 截图、键盘走查、280px 溢出、减少动效。
- **跨平台、跨 Agent。** Web、iOS、Android、桌面壳；支持 Claude Code、Codex、Cursor、Gemini CLI、Windsurf、Aider 或普通 LLM。

## 安装

把整个目录放进 Agent 的技能目录，或直接让 Agent 读 `SKILL.md`。

```bash
git clone https://github.com/dream120526-create/quiet-axis.git
```

常见位置：

| Agent | 路径 |
| --- | --- |
| Claude Code | `~/.claude/skills/quiet-axis/` 或 `<项目>/.claude/skills/quiet-axis/` |
| Codex | `~/.agents/skills/quiet-axis/` 或 `<项目>/.agents/skills/quiet-axis/` |
| Gemini CLI / Antigravity | `~/.gemini/config/skills/quiet-axis/` |
| Cursor | `<项目>/.cursor/skills/quiet-axis/`，或在 `.cursor/rules` 里加一行指针 |
| Windsurf | `<项目>/.windsurf/skills/quiet-axis/` |
| 其他 Agent | 目录放哪都行，在 `AGENTS.md` 加一句："做 UI 前先读 `quiet-axis/SKILL.md`。" |

没有运行时、没有 MCP、没有构建步骤。

## 怎么用

```text
读 quiet-axis/SKILL.md，做一个账单设置页。
先写 DESIGN.md 草案，再按七步执行，不许跳过分类。
```

小改动：

```text
用 quiet-axis。把主按钮颜色改掉，只检查令牌对比度（P0 9）。
```

技能里定义了改动分级，Agent 不会为了改一句文案重做整站。

## 设计原则

1. **逻辑先于像素。** 分类、结构、层级，最后才是视觉打磨。
2. **一屏一个主角、一个主动作。** 其余全部降级。
3. **动效解释状态变化。** 删掉不损失信息，就删掉。
4. **没证据不算通过。** 截图、计算对比度、键盘、减少动效。
5. **只做一个方向，并用一句话说清。** "现代简洁"不是方向。

## 仓库结构

```text
quiet-axis/
├── SKILL.md
├── DESIGN.md
├── CHEATSHEET.md
├── LICENSE
├── NOTICE
├── README.md
├── README.zh-CN.md
├── README.ja.md
├── README.es.md
├── topics/
│   ├── motion.md
│   ├── spring.md
│   ├── frameworks.md
│   ├── patterns.md
│   ├── forms.md
│   ├── states.md
│   ├── platforms.md
│   ├── i18n.md
│   ├── delivery.md
│   ├── recipes.md
│   └── reference.md
└── references/
    └── agent-ui-skills.md
```

## 许可证与署名

**MIT 许可证。** 可以复制、修改、再分发，也可以商用（包括放进付费产品）；保留版权声明即自动满足双作者署名。完整条款见 [LICENSE](LICENSE)。

作者：[dream120526-create](https://github.com/dream120526-create) 与 [appledream120526-cloud](https://github.com/appledream120526-cloud)。

第三方方法论引用与 CC BY 4.0 署名：[NOTICE](NOTICE)。
