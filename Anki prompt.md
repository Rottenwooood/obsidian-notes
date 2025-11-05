疑似有问题

--- 
你把上边的内容总结为一个Anki卡片，符合以下格式

要求如下：
1. 判断内容属于以下哪种类型，并使用对应模板：
   - 🔧 工程问题（解决某个报错、架构设计、部署问题等）
   - 📚 零散知识点（概念解释、语法细节、原理说明等）
   - 💡 灵感或想法（产品创意、技术构想、改进思路等）
   - 📄 论文精讲（论文核心思想、方法、评价等）

2. 使用以下字段格式输出（用三个反引号包裹 JSON，不要额外解释）：

🔧 工程问题：
```json
{
  "Type": "Engineering Q&A",
  "Question": "问题描述（简洁明了）",
  "Solution": "解决方案步骤或思路",
  "Code": "相关代码片段（无则留空）",
  "Context": "适用场景/技术栈（如：React + Vite 项目）",
  "Tags": "#bug #frontend #network"
}
```

📚 零散知识点：
```json
{
  "Type": "Knowledge Snippet",
  "Concept": "概念名称",
  "Explanation": "清晰解释（避免术语堆砌）",
  "Example": "示例（代码/句子/图示说明）",
  "Tip": "记忆提示或易错点",
  "Tags": "#css #layout #sticky"
}
```

💡 灵感或想法：
```json
{
  "Type": "Idea Note",
  "Idea": "核心想法一句话概括",
  "UseCase": "适用场景或用户需求",
  "NextSteps": "下一步可执行动作",
  "Related": "关联技术/项目/论文",
  "Tags": "#product #ai #feature"
}
```

📄 论文精讲：
```json
{
  "Type": "Paper Summary",
  "Title": "论文标题（英文）",
  "KeyInsight": "最核心的洞见或创新点",
  "Method": "方法简述（模型/算法/实验设计）",
  "Result": "主要结果或性能提升",
  "Critique": "局限性或个人评价",
  "Tags": "#paper #transformer #efficient"
}
```

3. 所有字段使用中文，但标签用英文 `#xxx` 格式。
4. `Code` 和 `Example` 中可使用 Markdown 代码块。
5. 不要添加其他文字，只输出卡片 JSON。
```
