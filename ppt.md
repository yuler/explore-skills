# Agent Skills

## 简介

首页他是 Anthropic 定义的一个规范 <https://agentskills.io/>

官网的定义:

> A simple, open format for giving agents new capabilities and expertise.

## 什么是 SKILL

他的本质是一个包含 `SKILL.md` 文件的文件夹

```bash
my-skill/
├── SKILL.md          # Required: instructions + metadata
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation
└── assets/           # Optional: templates, resources
```

SKILL.md 需要包含元数据 **metadata** (name, description) 以及 指令 **instructions** 还可以包含一些其他 **resources** 资源

元数据: 主要名称, 简短的描述

指令:

- Self-documenting: 自描述, 主要用自然语言描述这样作者和用户都能理解，可以很容易评审和优化
- Extensible: 可扩展, 技能可以从简单的文本指令 (Pormpt) 到复杂代码执行，资源和模板管理
- Portable： 可移植，SKILL 就是一个文件夹，所以他可以很轻松的修改，版本迭代 和分享

其他资源:

- scripts: 任意语言的可执行代码，Agent 来调用
- references: 可参考的文档，主要是 Agent 在执行任务的时候可以阅读参考的文档， 例如财务文档，法律文档
- assets: 一些静态资源，例如模板，图片，静态数据源

## SKILL 是如何工作的？

SKILL 在 Agent 上下文中采用[渐进式披露 (Progressive Disclosure)](https://agentskills.io/specification#progressive-disclosure)

- Discovery: 发现, Agent 启动的时候， 只加所有 SKILL 的元数据 (name, description), Agent 只需要在执行任务的时候知道什么改用相关的 SKILL
- Activation: 激活，当 Agent 执行任务的时候上下文匹配到了和元数据描述相符合的 SKILL, 这个时候再去加载完整的 SKILL.md 到上下文中
- Execution: 执行, Agent 根据 SKILL.md 描述的具体指令来，按需加载引用文件 或 执行指定代码

## SKILL 规范和格式

SKILL.md Frontmatter, 必须存在: name, description, 还包含一些可选项: license, compatibility, metadata, allowed-tools

```md
---
name: pdf-processing
description: Extract text and tables from PDF files, fill forms, merge documents.
license: Apache-2.0
metadata:
  author: example-org
  version: "1.0"
---
```

SKILL Body content, 没有太严格的格式要求，任务让 Agent 有效执行任务指令就可以

推荐包含的部分: 分步操作说明，输入输出示例，常见边界情况

可选目录:

scripts/
包含智能体可执行的代码。脚本应当：

- 自包含，或清晰注明依赖项
- 包含有用的错误提示
- 优雅地处理异常和边界情况
  所支持的语言依赖于代理实现，常用的包括 Python、Bash、JavaScript 等。

references/
包含供智能体按需查阅的额外文档资料：
REFERENCE.md - 详细的技术参考文档
FORMS.md - 表单模板或结构化数据格式
领域专用文档（如 finance.md、legal.md 等）
建议将参考资料文件拆分得更细致。智能体按需加载，更小的文件可以节省上下文资源。

assets/
包含静态资源：
模板（文档模板、配置模板）
图片（流程图、示例图片）
数据文件（查找表、数据结构、schema 等）

根据 **渐进式披露 (Progressive Disclosure)：** 原则, 推荐遵寻如下规则，在上下问使用中更高效

- Metadata: (~100 tokens)
- Instructions: (< 5000 tokens recommended)
- Resources: (as needed) 包含 scripts/, references/ 或者 assets/

保持 SKILL.md 下雨 500 行， 详细的具体指令可以放到 references 去根据不同的内容来拆分

通过 [skills-ref](https://github.com/agentskills/agentskills/tree/main/skills-ref) 工具来验证 SKILL 命名约定

```bash
skills-ref validate ./my-skill
```

## VS MCP

Agent Skill 是“你会干什么”，而 MCP 是“你如何与外界沟通的语言标准”。


- **Agent Skills (指令集)：** 它是 Agent 的**“技能手册”**。通常是一个本地的 Markdown 文件，用自然语言告诉 Agent：_“如果你要干 X，就按照 A->B->C 的步骤去执行。”_
- **MCP (接口标准)：** 它是 Agent 的**“标准插座”**。它用代码定义了一个稳定的通道，让 Agent 能安全地访问数据库、Docker 或远程服务器，解决**“怎么连”**的问题。

| 维度           | Agent Skills (核心是指令)        | MCP (核心是连接)                        |
| -------------- | -------------------------------- | --------------------------------------- |
| **形式**       | **.md 文件**（自然语言定义逻辑） | **JSON-RPC 服务**（代码定义接口）       |
| **编写门槛**   | **极低**。懂业务逻辑就能写。     | **中等**。需要写程序、打镜像。          |
| **运行位置**   | **本地工作区**。随项目代码走。   | **隔离环境**。如 Docker 或远程 Server。 |
| **执行成功率** | 依赖 LLM 实时推理，环境差异大。  | **极高**。环境标准化，屏蔽系统差异。    |

---

具体的实战例子

1. **代码重构 (Skill 胜出)：**

- **Skill：** 你在项目里放个 `refactor.md`，写着：“用 Rails 7 的新语法重构这个 Controller”。Agent 读了指令直接在本地改代码。**不需要写任何代码来驱动这个过程。**

2. **生产环境数据库查询 (MCP 胜出)：**

- **MCP：** 你部署一个 MCP Server 连接生产库。它提供了受控的 API。Agent 通过 MCP 协议发起请求。**因为涉及权限和复杂查询，用标准化的代码接口更安全、更稳定。**

---

两个形象化的比喻

1. **菜谱 (Skill) vs. 预制菜生产线 (MCP)**

- **Skill 是“菜谱”：** 告诉厨师（Agent）怎么做。厨师在自己厨房（本地环境）做，能不能做成看厨师发挥和厨房调料够不够。
- **MCP 是“预制菜线”：** 厨师只要按下按钮，工厂（Docker/Remote）就出一个标准口味的成品。不需要厨师动脑子，**成功率 100%**。

2. **游戏攻略 (Skill) vs. 游戏手柄 (MCP)**

- **Skill 是“攻略”：** 告诉玩家“先跳再开火”。玩家（Agent）利用游戏自带的动作（本地工具）去完成。
- **MCP 是“特制手柄”：** 给游戏增加了一个“一键大招”的实体按键。只要按下去，协议就会强制执行预设好的复杂动作。

---

## 快速上手如何使用 SKILL

<https://github.com/anthropics/skills>

我以 Claude 为为例子演示下， [brand-guidelines](https://github.com/anthropics/skills/tree/main/skills/brand-guidelines) 这个 Skill

如何使用 Claude, [方舟 Coding Plan 新用户 8.9 体验](https://volcengine.com/L/AeVoPo343XM/)

``bash
/plugin marketplace add anthropics/skills
/plugin install example-skills@anthropic-agent-skills

# prompt
帮我写一个简单的 HTML/CSS 卡片，用来展示 "AI 安全原则"，请使用 brand-guidelines 这个 skill 来设计样式, 文件输出到 ai.html

open ai.html
```

## 其他 agent

我们去官网首页可以看见 <https://agentskills.io/home#adoption> 基本上主流 Agent 都支持 Skill

[OpenCode Skills](https://opencode.ai/docs/skills/#place-files)
[Cursor Skills](https://cursor.com/docs/context/skills#skill-directories)
[Antigravity Skills](https://antigravity.google/docs/skills#where-skills-live)

但是现在有个问题，每个 IDE/Agent 工具都支持 Skills ，但是需要放到不同的目录下，对目录有不同的规则

这个时候，我们可以用 [skills](https://npm.im/skills) 工具——它本质上是一个命令行安装器，通过运行 `npx skills add ...`，会帮你自动下载并放置各类 Skill 到不同 Agent 所需的指定目录，无需手工复制移动，自动适配支持主流 Agent 平台的目录结构。


```bash
npx skills add vercel-labs/agent-skills --skill web-design-guidelines
# or
npx skills add https://github.com/michalparkola/tapestry-skills-for-claude-code/tree/main/article-extractor
# or
npx skills add https://github.com/michalparkola/tapestry-skills-for-claude-code/tree/main/article-extractor -g -a opencode -y

```

使用 article-extractor skill https://rauchg.com/2014/7-principles-of-rich-web-applications 提取这个文章, 并且翻译成中文, 文件保存到 output/opencode.md 下


## find some skills

- <https://skills.sh/>
- <https://github.com/ComposioHQ/awesome-claude-skills>


## 如何编写一个简单的 SKILL

git-release

```md
---
name: git-commit
description: Create a human-readable commit include emoji
---

## What I do

- Generate a human-readable commit message include emoji

## When to use me

- Use it when need git commit
```
---
