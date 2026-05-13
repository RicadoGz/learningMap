# LearningMap

`LearningMap.skill` 是一份面向代码仓库学习场景的规范型说明文件。它的目标不是“生成一张好看的架构图”，而是要求代理先真实阅读仓库，再产出一套可交互的 HTML 学习页，让读者只靠页面就能看懂项目的运行路径、函数链路、关键代码和数据流。

`LearningMap.skill` is a specification for repository learning and onboarding. Its goal is not to generate a decorative architecture diagram, but to require an agent to read the repository first and then produce a set of interactive HTML learning pages so a reader can understand runtime flow, function chains, key code, and data flow directly from the pages.

## 仓库内容 | Repository Contents

- `LearningMap.skill`：核心规范  
  `LearningMap.skill`: the core specification
- `LICENSE`：许可证  
  `LICENSE`: license information

## 它解决什么问题 | What It Solves

当用户提出“读懂这个仓库，并生成一个可视化学习页”时，`LearningMap.skill` 会约束生成结果必须围绕真实代码路径展开，而不是停留在 README 摘要或静态示意图层面。

When a user asks to understand a repository and generate a visual learning page, `LearningMap.skill` constrains the output so it is built around real code paths rather than a README summary or a static diagram.

它强调三件事：

It emphasizes three things:

- 先分析真实入口和运行路径  
  Analyze real entry points and runtime paths first
- 再按路径生成可交互页面  
  Then generate interactive pages organized by path
- 解释必须贴着代码语义，而不是解释基础语法  
  Explanations must focus on code meaning in the flow, not basic syntax

## 预期产物 | Expected Deliverables

规范要求至少生成以下文件：

The spec requires at least the following files:

- 一个主页面，默认名为 `repo_flow_map.html`  
  One main page, typically named `repo_flow_map.html`
- 多个路径详情页，默认名为 `repo_flow_path_<slug>.html`  
  Multiple path detail pages, typically named `repo_flow_path_<slug>.html`
- 一个解释数据文件，默认名为 `docs/flow_explainers.json`  
  One explanation data file, typically named `docs/flow_explainers.json`

这些文件名不是死板写死的，但命名需要能反映真实路径和页面职责。

These filenames are not rigidly hardcoded, but the names should reflect real paths and page responsibilities.

## 主页面要求 | Main Page Requirements

主页面承担“导航 + 总览”的职责，至少要满足这些要求：

The main page acts as navigation plus overview and should satisfy at least these requirements:

- 使用真正的 SVG 流程图，不能只用 `->` 或 `→` 文本代替  
  Use a real SVG flowchart, not plain text arrows such as `->` or `→`
- 流程图中至少包含开始节点、处理节点、判断节点、输出节点  
  The flowchart must include at least a start node, process node, decision node, and output node
- 必须展示至少一条分支路径  
  It must show at least one branching path
- 提供基于真实运行路径生成的卡片区，卡片数量不能预设死  
  Include a card section based on real runtime paths, with no fixed card count
- 每张卡片都要能跳转到对应的路径详情页  
  Every card should link to its corresponding path detail page
- 提供源码阅读辅助区，至少包含推荐阅读顺序、关键产物索引、术语速查  
  Include a source-reading helper section with recommended reading order, key artifact index, and term quick reference

另外，主页面不应该塞入低价值的背景说明，而应聚焦学习入口和导航价值。

The main page should avoid low-value explanatory clutter and stay focused on learning entry points and navigation value.

## 路径详情页要求 | Path Detail Page Requirements

每个路径详情页都必须采用统一的四栏布局，从左到右依次为：

Each path detail page must use a unified four-column layout, from left to right:

1. 功能步骤列  
   Functional steps
2. 子函数链路列  
   Sub-function chain
3. 代码列  
   Code
4. 解释列  
   Explanation

交互上也有明确要求：

The interaction model is also explicitly defined:

- 支持 `Previous Step / Next Step`  
  Support `Previous Step / Next Step`
- 支持点击步骤直接切换内容  
  Support direct step switching by clicking a step
- 步骤切换时，函数链、代码、解释要联动刷新  
  When a step changes, the function chain, code, and explanation must update together
- 子函数链要支持多级展开  
  The sub-function chain must support multi-level expansion
- 点击任意函数节点时，代码和解释要同步更新  
  Clicking any function node should refresh both code and explanation

代码展示方面，规范特别强调：

For code display, the spec emphasizes the following:

- 保留原始缩进，尤其是 Python  
  Preserve original indentation, especially for Python
- 使用等宽字体  
  Use a monospace font
- 最好展示行号  
  Line numbers are recommended
- 优先展示完整函数，而不是只有签名  
  Show full function bodies rather than only signatures

## 解释数据文件格式 | Explanation Data Format

解释内容必须从 JSON 加载，且结构固定为：

Explanation content must be loaded from JSON, using the following fixed structure:

```json
{
  "function_overview": {
    "file.py:function_name": "The role of this function in the flow"
  },
  "line_guides": {
    "file.py:function_name": [
      {
        "match": "code fragment string",
        "explain": "what this fragment does, explained by its flow meaning"
      }
    ]
  }
}
```

这里最重要的约束是：

The most important constraints are:

- `match` 必须是稳定可匹配的代码片段  
  `match` must be a stable code fragment that can be reliably found
- `explain` 必须解释“这段代码在流程里做什么”  
  `explain` must describe what the fragment does in the flow
- 不能把解释浪费在 `return`、`=` 这类低信息量语法上  
  Explanations should not be wasted on low-information syntax like `return` or `=`

## 推荐执行流程 | Recommended Analysis Process

`LearningMap.skill` 要求代理至少按下面的顺序分析仓库：

`LearningMap.skill` expects the agent to analyze the repository in roughly this order:

1. 找出入口，如 CLI、API、script、worker、scheduler、test  
   Identify entry points such as CLI, API, scripts, workers, schedulers, or tests
2. 识别真实运行路径，而不是只看目录名猜测  
   Identify real runtime paths rather than guessing from folder names
3. 为每条路径命名，并生成可用于文件名的 slug  
   Name each path and create a slug suitable for filenames
4. 追踪函数调用链和关键数据对象  
   Trace function call chains and key data objects
5. 明确输入、转换、输出  
   Confirm input, transformation, and output
6. 按真实路径数量生成详情页  
   Generate detail pages based on the actual number of paths
7. 把高价值解释写入 `flow_explainers.json`  
   Write high-value explanations into `flow_explainers.json`

## 验收标准 | Acceptance Criteria

最终结果至少应该满足：

The final output should satisfy at least the following:

- 主页面流程图是真正的 SVG 流程图  
  The main page flowchart is a real SVG flowchart
- 所有路径详情页都能正常打开  
  All generated path detail pages can be opened
- 至少主路径页实现完整的四栏联动  
  At least the primary path page has full four-column linkage
- 代码区保留原始缩进  
  The code area preserves original indentation
- 解释区由 JSON 驱动，而不是模板化空话  
  The explanation area is driven by JSON rather than generic template text
- 用户不回到编辑器，也能通过页面理解主链路  
  A user should be able to understand the main flow without returning to the editor

## 明确禁止的事情 | Explicitly Forbidden

这个规范明确反对以下做法：

The specification explicitly forbids the following:

- 只做装饰性的架构图  
  Producing only a decorative architecture diagram
- 只复述仓库 README  
  Merely summarizing the repository README
- 只列函数名，不展示代码  
  Listing function names without showing code
- 逐行解释低价值语法  
  Explaining low-value syntax line by line
- 做成静态死图，没有交互  
  Producing a static dead image with no interaction
- 不读仓库就预设固定路径数量和路径名称  
  Presetting fixed path counts and path names without reading the repository

## 使用方式示例 | Example Prompt

如果你想把它作为生成规则来用，可以给代理类似这样的任务：

If you want to use it as a generation rule, you can prompt the agent like this:

```text
请先阅读这个仓库，识别真实运行路径，然后按 LearningMap.skill 的要求生成：
1. 一个主页面
2. 每条真实路径对应的详情页
3. 一个 flow_explainers.json
要求页面可交互，并且代码与解释联动。

Please read this repository first, identify the real runtime paths, and then generate the following according to LearningMap.skill:
1. One main page
2. One detail page for each real runtime path
3. One flow_explainers.json
The pages should be interactive, and the code and explanations should stay linked.
```

## 总结 | Summary

`LearningMap.skill` 的本质是一份“仓库学习页生成规范”。它把重点放在真实代码路径、可交互阅读体验和高价值代码解释上，适合用来把复杂仓库整理成面向学习和 onboarding 的可视化入口。

At its core, `LearningMap.skill` is a repository learning-page specification. It focuses on real code paths, interactive reading experience, and high-value code explanations, making it well suited for turning complex repositories into a visual learning and onboarding entry point.
