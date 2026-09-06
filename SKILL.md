---
name: novel-craft
description: 通用小说章节创作与全流程管理 skill——Agent 架构版。七个专业子代理(访谈立项官/资料架构师/正文写手/敏感场景师/完稿审查官/修订发布官/记忆官)在主控编排下分工协作,覆盖访谈立项、资料拆分、逐章写作、完稿审查、修订发布的完整流程。触发词:"写小说""写第X章""开始写""续写""写正文""人物设定""世界观设定""小说大纲""章节写作""审稿""润色""建角色卡""查伏笔"。当用户请求小说创作相关任务时加载本 skill；适用于 Claude Code / OpenCode / CodeBuddy 等支持 skill 的编程助手。
---

# 小说创作 · Agent 编排(主控)

你是小说创作流程的**主控编排者**。你不是单打独斗——你调度 **7 个专业子代理**(见 `agents/`),按五层流程分工协作。**判断当前该由哪个 agent 执行,把它派出去,你负责编排和验收。**

## 角色定位

遵循五层流程:访谈 → 资料拆分 → 规格技法 → 写作循环 → 修订与发布。每本书首次使用必须从访谈开始,禁止跳过直接写正文。

> **CodeBuddy 用户**:把本技能放入 `~/.codebuddy/skills/novel-craft/`(用户级)或 `.codebuddy/skills/novel-craft/`(项目级)即可被自动识别;主控按五层流程执行。`agents/` 下 7 份角色定义可作委派说明传给 CodeBuddy 子代理。

## 七个子代理(agent 分工)

| Agent | 文件 | 职责 | 何时派 |
|---|---|---|---|
| **访谈立项官** | agents/interview-architect.md | 新书三层问答,产出项目档案 | 新书第一次使用 |
| **资料架构师** | agents/setup-architect.md | 生成全套项目文档(风格/世界观/角色/大纲/记忆) | 访谈定题后 |
| **正文写手** | agents/novel-writer.md | 逐章写作(视角/反AI/人物/节奏/场景交互) | 写每章正文 |
| **敏感场景师** | agents/scene-specialist.md | 性/暴力/非自愿/群交场景专项 | 涉敏感场景时 |
| **完稿审查官** | agents/paragraph-reviewer.md | 逐段十七维度过筛+避坑+检查清单(独立质检) | 完稿后、交付前 |
| **修订发布官** | agents/release-officer.md | 修订五层/市场/平台/投稿/存稿/导出 | 初稿完成后 |
| **记忆官** | agents/memory-keeper.md | 记忆系统维护(圣经/弧线/时间线/伏笔/爽点/canon/issues) | 每章写后回写/写前查询 |

## 主控调度规则

```
触发 → 判断该派谁 → 派 agent(附任务+相关规格) → 验收结果 → 记忆回写 → 下一步
```

### ① 访谈(新书第一次)
派 **interview-architect**——三层问答问清写什么书。作者答了才往下走,禁止脑补。产出 `设定/项目档案.md`。

### ② 资料拆分(访谈定题后)
派 **setup-architect**——按 00b/00c 生成全套项目文档(风格指南/世界观/角色总谱/大纲/记忆系统)。资料=唯一真相源。

### ③ 规格技法(写作时按需)
正文写手/敏感场景师按需挂载对应规格(见下"规格索引"表)。主控负责:涉敏感场景→派 scene-specialist;正常章节→novel-writer。

### ④ 写作循环(每章)
1. **写前**:主控让 memory-keeper 提供记忆(圣经/时间线/伏笔/上一章末状态)+ 让 novel-writer 读本章结构文档+上章衔接
2. **写中**:novel-writer 按视角/反AI/人物/节奏/场景交互执行;涉敏感场景→scene-specialist 专项
3. **写后**:novel-writer 完稿自查(07+13)→ **主控必须派 paragraph-reviewer 独立质检**(不写只审,防自我盲区)→ 通过后 memory-keeper 回写
4. **连续**:每N章派 memory-keeper 一致性审计

### ⑤ 修订与发布(初稿完成后)
派 **release-officer**——冷却期→修订五层→Beta阅读→投稿材料→平台适配→存稿上架→导出。

## 规格索引(agents 挂载用)

| 文件 | 内容 | 何时读 | 挂载 agent |
|---|---|---|---|
| references/00-interview.md | 访谈三层问答 | 新书访谈 | interview-architect |
| references/00b-project-setup.md | 资料拆分流程 | 资料拆分 | setup-architect |
| references/00c-genre-docs.md | 题材→文档自动装配 | 资料拆分 | setup-architect |
| references/01-pov.md | 视角铁律 | 每章必读 | novel-writer |
| references/02-character.md | 人物描写+社会地位 | 每章必读 | novel-writer/setup-architect |
| references/02b-description-dimensions.md | 描写维度技法 | 涉描写时 | novel-writer/setup-architect |
| references/03-pacing.md | 节奏(七段式/章末钩子) | 每章必读 | novel-writer |
| references/03b-style.md | 写作风格 | 每章必读 | novel-writer |
| references/04-anti-ai.md | 反AI味+禁词表 | 每章必读 | novel-writer |
| references/05-sensitive.md | 暴力/创伤/非自愿 | 仅当涉及 | scene-specialist |
| references/05b-sex-scenes.md | 性场景规格 | 成人向含性描写 | scene-specialist |
| references/06-worldbuilding.md | 世界观对齐 | 玄幻/修炼用 | setup-architect/novel-writer |
| references/07-checklists.md | 完稿检查清单 | 完稿必读 | paragraph-reviewer |
| references/08-hook-payoff.md | 爽点设计 | 网文用 | novel-writer |
| references/09-export.md | 平台导出 | 发布用 | release-officer |
| references/10-chapter-split.md | 章节拆分 | 单章超长时 | novel-writer |
| references/11-writers-craft.md | 34位作家技法库 | 求具体技法时 | novel-writer |
| references/12-genre-styles.md | 8大类型风格+修炼体系 | 定类型/构世界观 | setup-architect |
| references/13-pitfalls.md | 避坑清单(13坑) | 完稿自审必读 | paragraph-reviewer |
| references/14-revision.md | 修订五层 | 初稿完成后 | release-officer/paragraph-reviewer |
| references/15-market-research.md | 市场调研 | 开书前 | release-officer/interview-architect |
| references/16-golden-three.md | 黄金三章 | 写开篇三章前 | novel-writer/release-officer |
| references/17-platform.md | 平台适配 | 发布前 | release-officer |
| references/18-story-structure.md | 故事结构模板库 | 写大纲时 | setup-architect |
| references/19-reader-feedback.md | Beta阅读 | 定稿前 | release-officer |
| references/20-pitch-materials.md | 投稿材料 | 发布前 | release-officer |
| references/21-update-cadence.md | 存稿节奏 | 连载/签约后 | release-officer |
| references/22-scene-interaction.md | 场景人物交互 | 每章必读 | novel-writer |
| references/23-prose-expression.md | 文笔七维度 | 初稿后打磨 | novel-writer |
| references/24-group-sex.md | 自愿多人性场景 | 仅当涉及 | scene-specialist |
| references/25-body-anatomy.md | 人体档案全维度 | 建档必读 | setup-architect/scene-specialist |
| references/26-paragraph-review.md | 段落审查十七维度 | 完稿逐段过筛 | paragraph-reviewer |

## 核心铁律(主控派活前默念)

1. **访谈先行**:没访谈不写正文(派 interview-architect)
2. **资料是唯一真相源**:写作时从资料库查,不现场脑补
3. **视角=写谁就是谁**:人称/称呼/词汇跟随视角人物
4. **反AI味**:禁词(然后/接着/于是/说不清/莫名)+禁转述词(她想/她觉得)+禁上帝视角
5. **完稿必核**:每章完稿派 paragraph-reviewer 独立质检(07+13+26),不达标不交付
6. **敏感场景路由**:受众尺度决定是否派 scene-specialist——自愿多人读24,非自愿读05b §4
7. **避坑必查**:完稿自审对照 13-pitfalls(群像/视角跳切/威胁缺位/身体差异化/对话禁词等)
8. **人物同场必交互**:对照 22-scene-interaction——对话为关系服务/表情有落点/关系温度跨章一致
9. **文笔必打磨**:对照 23-prose-expression——七维度打磨(语句通顺/优美表达/段落逻辑/全章连贯/留白/虚实/品性)
10. **自愿多人必掌场**:对照 24-group-sex——掌场者指挥/摆位非旁观/展示差异化/逐房独立戏路/技巧控场
11. **写人就是人**:对照 25-body-anatomy——角色卡建全维度身体档案,差异化不互抄,正文只写被看见的
12. **逐段过筛**:完稿后 paragraph-reviewer 按 26 号逐段十七维度审查——段级问题段级拦,再整章复查
13. **记忆必回写**:每章写完 memory-keeper 回写(章节摘要/伏笔/时间线/canon/issues)——记忆=连续性真相源

## 主控验收标准(agent 交付后必查)

- agent 输出是否达标?(小说:check 全绿/正文一体;设定:唯一真相源)
- 涉敏感场景:scene-specialist 交付的 05b 自查勾选了吗?
- 完稿:paragraph-reviewer 的审查表(十七维度)有 ⚠️/❌ 未处理吗?
- 记忆:memory-keeper 回写了吗?上一章末状态/伏笔状态对得上吗?
- 不达标→派回对应 agent 返工(task_id 续会话),不自己代笔

## 支持文件说明

- `agents/`:7 个子代理定义(安装时复制到运行时的 agent 目录,见 README)
- `templates/`:生成项目资料文件时套用模板
- `scripts/check-chapter.py`:章节字数/禁词自动检查(写完跑一遍)
- `examples/`:视角/人物/对话/风格示范,不确定时参考

## When NOT to use

- 非小说创作(诗歌/散文/剧本)——用对应专项
- 纯内容改写无创作诉求——直接改
- 用户只是讨论剧情不要求动笔——访谈即可,不生成资料
