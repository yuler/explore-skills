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

SKILL.md 需要包含元数据 **metadata** (name, descrption) 以及 指令 **instructions** 还可以包含一些其他 **Resources** 资源

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


[渐进式披露 (Progressive Disclosure)](https://agentskills.io/specification#progressive-disclosure)

- Discovery: 发现, Agent 启动的时候， 只加所有 SKILL 的元数据 (name, descrption), Agent 只需要在执行任务的时候知道什么改用相关的 SKILL


- Metadata (~100 tokens):
- Instructions (< 5000 tokens recommended): 

保持 SKILL.md 下雨 500 行， 详细的具体指令可以放到 references 去根据不同的内容来拆分


* **1. 揭秘 Agent Skills**

* 什么是 Agent Skills？（核心定义）

如果把大模型比作一个**“聪明的大脑”，那么 Agent Skills 就是这个大脑的“五官”和“手脚”**。

本质： Agent Skills 是赋予 LLM 访问外部世界、执行特定任务的一组工具接口。

突破： 原始的 LLM 只能基于训练数据（即“记忆”）进行对话。有了 Skills，它就能处理实时数据（如今天的天气）、私有数据（如你的记事本）和执行动作（如发送一封邮件）。




---

### Slide 1.2: 运行原理：从 LLM 到 Skill 的驱动

* **感知与触发 (Perception)：**
* LLM 并不“预载”所有 Skill 的实现细节，而是通过轻量级的 **元数据 (Metadata)** 感知。这就像大脑知道书架上有哪些书，但只有在需要时才会去翻开某一页。

* **Function Calling / Tool Use 的角色：**
* **语义匹配：** 当用户意图与某 Skill 的描述（Description）产生语义碰撞时，LLM 发起调用。
* **结构化输出：** 模型不再输出废话，而是精准生成符合格式要求的 JSON 指令。


* **执行机制：从指令到动作：**
* **渐进式披露 (Progressive Disclosure)：** 在一些先进架构（如 Claude 或某些 Agent 框架）中，Skill 可以是物理文件（Markdown 或脚本）。LLM 通过执行 Bash 命令或读取文件来动态加载逻辑，避免了上下文污染。


* **逻辑闭环 (Closed Loop)：**
* **Observation（观测）：** 执行结果（API 返回值、日志等）被喂回给 LLM。
* **迭代推理：** LLM 根据“观测”到的事实，决定任务是完成了，还是需要调用下一个 Skill。

---

### Slide 1.3: 拆解 Skill：构成它的“三位一体”

基于 `agentskills.io` 的理念，一个优秀的 Skill 不是一段死代码，而是一个**“可被理解的原子能力”**。我们可以用更接地气的方式来描述它的三要素：

#### 1. 身份标识 (Name) —— “你是谁？”

* **技术层面：** 函数名或 ID（如 `get_weather`）。
* **人话版：** 就像一个人的名字。在 Agent 的工具库里，它必须是唯一的，方便模型快速索引。

#### 2. 调用说明书 (Description) —— “你有什么用？”

* **技术层面：** 用于语义搜索和模型判断的自然语言描述。
* **人话版：** 这是 Skill 的**“灵魂”**。你要告诉 AI 这个工具在什么场景下能救命。
> **避坑指南：** 优秀的描述不是写“获取数据”，而是写“当用户询问特定地点的实时天气或未来 3 天预报时，请调用此工具”。

#### 3. 数据契约 (Input Schema) —— “我该传什么？”

* **技术层面：** JSON Schema 定义（参数类型、必填项、枚举值）。
* **人话版：** 这是一个**“填空题模板”**。它规定了 AI 必须提供哪些精确信息（比如：`city_name` 必须是字符串），才能成功触发后续的代码逻辑。



打一个比方：

> “如果 Agent 是一个厨师，**Name** 就是灶台上的标签，**Description** 是菜单上的菜名和口味介绍，而 **Schema** 就是这道菜必须准备的食材清单。缺了任何一个，大厨都没法开火。”

**针对“执行机制”里的“渐进式披露”，你想让我为你准备一个具体的例子（比如 AI 怎么通过读一个文件来加载 Skill）吗？这在演示中会非常硬核。**

---

这一部分的重点在于展示 **“从知到行”** 的跨越。如果说第一部分是在讲“大脑构造”，那么第二部分就是在讲“超能力清单”。

以下是为你拆解的 PPT 章节建议：

---

### Slide 2.1: 能力边界：Agent Skill 能干什么？

* **从“静态记忆”到“实时触达”：** 突破预训练数据的时间限制。
* **从“信息孤岛”到“数据互联”：** 打破公共互联网与私有数据之间的屏障。
* **从“语言模型”到“行动实体”：** LLM 不再只是“建议者”，而是“执行者”。

---

### Slide 2.2: 维度一：深度信息获取 (Information Retrieval)

* **打破“断网”尴尬：**
* 访问实时搜索（Google/Bing Search），获取今日新闻、即时天气、股市波动。


* **私有知识库连接：**
* 通过 API 调取个人笔记、企业内部文档或用户的 Gmail 邮件。


* **数据库直连：**
* **示例：** 作为一个程序员，你可以写一个 Skill 让 Agent 直接查询你的 Rails 后台数据库。
* *场景：* “帮我查一下上周注册且未激活的用户有多少？”


---

### Slide 2.3: 维度二：动作执行与反馈 (Action Execution)

* **写权限的释放：**
* Skill 不仅能“读”，更能“写”。它拥有了对外部系统的 **CRUD (增删改查)** 能力。


* **自动化操作演示：**
* **开发流：** “帮我把这个 Bug 总结一下，并在 GitHub 上创建一个 Issue。”
* **日常流：** “帮我给王经理发封邮件，约明天下午两点开会。”


* **SaaS 联动：**
* 操作 Stripe 处理退款、操作 Notion 更新页面、操作你的 “beep” 系统发送推送通知。



---

### Slide 2.4: 维度三：流程自动化 (Workflow Automation)

* **逻辑编排 (Orchestration)：**
* Agent 能够分析复杂指令，并自动拆解为多个 Skill 的**链式调用**。


* **典型闭环案例：**
1. **检测 (Sense)：** 使用 Skill A 检查网站 SSL 证书是否到期。
2. **判断 (Decide)：** 发现到期时间不足 7 天。
3. **行动 (Act)：** 使用 Skill B 自动提交续费申请，并使用 Skill C 在 Slack 发送提醒。


* **从“单发子弹”到“全自动步枪”：** 这种串联能力是构建独立 SaaS Agent 的核心。

---

### Slide 2.5: 总结：Skill 让 Agent 具备“职场属性”

| 传统 Chatbot | 具备 Skill 的 Agent |
| --- | --- |
| 提供“建议” | 提供“结果” |
| 需要你手动复制粘贴 | 自动在系统间流转数据 |
| 知识止于训练日期 | 知识始于当下每一秒 |

---

### 💡 针对你背景的建议

在讲到 **Slide 2.3** 或 **2.4** 时，你可以顺带提一下你正在做的 **“beep”项目**。

> **小引子：** “比如我写了一个叫 `beep` 的通知系统。在没有 Skill 之前，我需要登录后台点发送；现在我给它写了一个 Agent Skill，我只需要对 AI 说：‘给所有 SSL 快过期的用户发个预警’，Agent 就会自动查库、过滤名单、通过 `beep` 接口把通知推出去。”

这种真实的案例会让听众瞬间理解 Skill 的生产力价值。

**你觉得这个维度的拆解是否够全面？如果没问题，我们要不要进入最精彩的“第 3 部分：Skill vs MCP 的大混战”？**

* **3. 架构对比：Agent Skill vs. MCP (Model Context Protocol)**
* **Agent Skill：** 平台专有的插件系统（如 OpenAI Actions, Gemini Extensions）。
* **MCP：** 开放的通用协议，实现“一套工具，到处运行”。
* **对比：** 封闭生态与开放标准的博弈。


* **4. 快速上手：操作演示**
* **添加 Skill：** 如何在现有平台中发现并配置 Skill。
* **实际使用：** 现场演示“对话即操作”的交互体验。
* **观察后台：** 实时展示 Agent 调用的 JSON 请求与返回结果。


* **5. 开发者实战：手写一个 Agent Skill**
* **定义 API 端点：** 构建一个简单的后端服务。
* **编写描述文件：** 如何写出让 AI 不产生“幻觉”的提示词。
* **本地调试：** 使用调试工具验证 Skill 的参数匹配与执行逻辑。


* **6. 深度联动：如何桥接 MCP**
* 通过 Agent Skill 访问现有的 **MCP Server**。
* 实现跨平台的工具共享与数据联动。


* **7. 最佳实践与避坑指南**
* **安全性：** 如何处理敏感数据的读写权限。
* **错误处理：** 当 API 失败或超时，如何引导 AI 进行优雅重试。
* **描述优化：** 提高 Skill 调用成功率的技巧。