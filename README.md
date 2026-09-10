# auto-balloon-fai-v2

一个面向工业首件检验（FAI）的 Codex skill：根据零件工程图 PDF 和检验模板，生成气泡标注图与对应检验表，并确保气泡号、检验行和检具映射来自同一份受控特性台账。V2 默认把每次调用隔离为独立新项目，只有用户明确指定基线时才继承历史数据。

## V2 变更

- 默认使用 `independent` 模式，不主动搜索或关联其他项目。
- 相同文件哈希不再被视为基线授权。
- 只有用户明确指定基线并要求继承时，才进入 `revision` 模式。
- 历史 ID 在继承前必须重新通过当前范围与禁区审计；冲突保持 `HOLD`，不得静默选择规则。
- 页面边界/非零引线和硬禁区分别作为独立质量门禁运行并留存内部结果。
- 独立项目首次交付统一从 `V1` 开始。

## 适用场景

- 首件检验（FAI）
- 来料检验与过程检验
- 机加工、钣金和医疗器械零部件图纸
- 尺寸、形位公差、螺纹、材料及明确属性要求的特性编号

本 skill 不用于普通 Excel 气泡图，也不把图纸外的假设伪装成设计要求。

## 核心能力

- 识别图纸中的尺寸、形位公差和独立检验特性。
- 建立唯一特性台账，统一驱动气泡 PDF、检验行和检具映射。
- 默认从零连续编号，并在用户明确授权修订模式时处理稳定编号和退役编号。
- 为气泡、引线和箭头定义禁区与碰撞检查，保护技术要求区、标题栏和修订栏。
- 区分显式公差、图纸声明的一般公差、用户指定标准和待批准工作假设。
- 对缺失定义、要求冲突、实际检具信息不足等情况保持 `OPEN`、`FOR REVIEW` 或 `HOLD`。
- 对 PDF 与 XLSX 执行结构检查、公式检查、渲染检查和逐页视觉复核。

## 默认交付物

除非用户明确要求其他内容，最终只交付：

1. 一份 FAI 气泡图 PDF。
2. 一份 XLSX，且只包含以下两个工作表，顺序固定：
   - `FAI检验记录`
   - `检具代码`

内部特性台账、坐标、渲染图和 QA 报告留在工作目录，不作为用户交付物。

## 安装

V2 是 [Slowpulp/auto-balloon-fai](https://github.com/Slowpulp/auto-balloon-fai) 仓库的当前版本，`main` 指向最新 V2。目录名和 skill 名均为 `auto-balloon-fai-v2`。可克隆到用户级 skill 目录：

```text
git clone https://github.com/Slowpulp/auto-balloon-fai.git $HOME/.agents/skills/auto-balloon-fai-v2
```

V1 保留在同一仓库的 `v1.0.0` 标签中。如需让 V1 和 V2 同时安装，可另外检出 V1：

```text
git clone --branch v1.0.0 --depth 1 https://github.com/Slowpulp/auto-balloon-fai.git $HOME/.agents/skills/auto-balloon-fai
```

Codex 通常会自动检测 skill 变更；如果未出现，请重启 Codex。安装位置与 skill 机制可参阅 [OpenAI 官方技能文档](https://developers.openai.com/codex/skills)。

## 运行要求

- 支持 agent skills 的 Codex 环境。
- 宿主环境具备 PDF 渲染、标注与复核能力。
- 宿主环境具备 XLSX 创建、公式重算与渲染检查能力。

本仓库是工作流说明型 skill，不包含可独立运行的气泡排布或工作簿生成命令行程序。

## 使用方式

在 Codex CLI 或 IDE 扩展中，可以通过 `$` 显式调用：

```text
$auto-balloon-fai-v2
请根据零件图纸 drawing.pdf 和检验模板 template.xlsx，生成 FAI 气泡图及双工作表检验表。
```

当任务描述与 skill 的适用范围明确匹配时，Codex 也可以自动选择它。

建议提供以下输入：

- 受控的零件工程图 PDF。
- 需要沿用布局和字段术语的检验模板。
- 图纸未明确时，经批准的一般公差标准、版本和等级。
- 用户明确指定的已发布气泡图或特性台账（仅在需要进入 `revision` 模式并保持历史编号时提供）。
- 真实检具信息、抽样要求、命名规则和交付版次要求。

## 工作流概览

1. 确定 `independent` 或 `revision` 模式和允许读取的输入范围。
2. 渲染并检查源图与模板，记录哈希、页数、页面几何、旋转和修订信息。
3. 提取并去重检验特性，建立唯一受控台账。
4. 确定禁区、编号策略、气泡位置、引线和锚点。
5. 依据显式要求和受控标准建立公差与状态。
6. 从同一台账生成气泡 PDF 和双工作表 XLSX。
7. 完成自动校验和高分辨率逐页复核后再交付。

## 重要边界

- 不伪造实测值、批次、检具型号、资产号、校准证据、批准或签字。
- 未注公差不得仅凭“常用”自行选定标准或等级；无法取得可靠表值时保持待确认。
- 模板控制表单布局和术语，不控制新零件的身份、材料、尺寸或实测数据。
- 默认不搜索或复用旧项目；相同源图哈希也不能替代用户的明确基线授权。
- 修订模式下，源图哈希、页面几何或旋转不一致时，不复用旧气泡坐标，并重新验证历史 ID 的语义映射。
- 结构验证通过只说明文件生成正确，不代表零件合格或已正式放行。

## 仓库结构

```text
auto-balloon-fai-v2/
├── AGENTS.md
├── CHANGELOG.md
├── SKILL.md
├── VERSION
├── agents/
│   └── openai.yaml
└── references/
    ├── inspection-workbook.md
    ├── quality-gates.md
    ├── tolerance-governance.md
    └── workflow.md
```

详细规则：

- [SKILL.md](SKILL.md)：skill 入口、默认交付契约和核心约束。
- [workflow.md](references/workflow.md)：FAI 气泡图的完整制作流程。
- [tolerance-governance.md](references/tolerance-governance.md)：未注公差、标准依据与冲突治理。
- [inspection-workbook.md](references/inspection-workbook.md)：双工作表结构、字段和判定逻辑。
- [quality-gates.md](references/quality-gates.md)：PDF、XLSX 与发布质量门禁。

## 许可证

本仓库当前未包含 `LICENSE` 文件。若需要允许他人复制、修改或分发，请由仓库所有者选择并添加合适的许可证。
