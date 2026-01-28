# explore-skills

> [skills](https://agentskills.io/home) are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks. 

## What's is skill

## Add skilss

Use npm [skills](https://npm.im/skills) or [add-skill](https://npm.im/add-skill) quick add skills

```bash
npx skills add https://github.com/vercel-labs/agent-skills/tree/main/skills/web-design-guidelines
# or
npx skills add vercel-labs/agent-skills --skill web-design-guidelines
# or
npx add-skill https://github.com/vercel-labs/agent-skills/tree/main/skills/web-design-guidelines
```

## Create a skill

TODO:

## References

- <https://github.com/anthropics/skills>
- <https://www.npmjs.com/package/add-skill>
- <https://www.npmjs.com/package/skills>
- <https://skills.sh/>


## TODO:

* **1. 揭秘 Agent Skills**
* 什么是 Agent Skills？（核心定义）
* 运行原理：LLM 是如何通过 **Function Calling（函数调用）** 驱动 Skill 的？
* Skill 的三要素：名称（Name）、描述（Description）、参数规格（Parameters/Schema）。


* **2. 能力边界：Agent Skill 能干什么？**
* **信息获取：** 突破训练数据时效，访问实时搜索、数据库或私有 API。
* **动作执行：** 自动化操作（如发送邮件、创建 GitHub Issue、更新 SaaS 后台数据）。
* **流程自动化：** 将多个 Skill 串联完成复杂任务。


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


## Others

- [别把整个 GitHub 装进 Skills，Skills 的正确用法](https://x.com/dotey/status/2014607139352859077)
- [Skill 不就是长一点的提示词吗？](https://x.com/dotey/status/2015212857374413040)
- [Skills 不就是脚本套个壳吗？我十几年前就玩 AutoIt 了](https://x.com/dotey/status/2015616465051742453)


- 智普
