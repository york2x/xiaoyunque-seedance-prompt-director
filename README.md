# 小云雀 Seedance 2.0 / 2.5 提示词导演

一个面向 **小云雀（Xiaoyunque）平台** 的 Seedance 2.0 / 2.5 视频提示词 Skill。

它不是简单套模板，而是根据模型版本、任务模式、参考素材类型、时长与控制强度，自动选择更适合小云雀的提示词结构，并处理：

- Seedance 2.0 / 2.5 模型路由
- Preflight 前置校验与风险分级
- 图片 / 视频 / 音频参考素材职责映射
- 连续时间轴与镜头节奏
- 人物动作、表情、对白与物理反馈
- BGM、环境音、动作音效、对白、字幕
- 转场与跨镜连续性
- 多人物数量、左右手、道具状态检查
- Postflight 编译后审核
- Prompt 诊断、压缩与负向控制

## 核心工作流

```text
User Brief
   ↓
Preflight
   ↓
Prompt Compiler
   ↓
Postflight
   ↓
Final Prompt
```

职责固定为：

- **Preflight validates the brief.** 检查用户需求、素材映射和结构是否可编译。
- **Prompt Compiler** 将通过校验的信息组织成适合 Seedance 2.0 / 2.5 的最终提示词。
- **Postflight validates the compiled prompt.** 检查最终 Prompt 是否重新引入冲突。

### Preflight

生成 Prompt 前先进行静态检查和规范化：

- `P0`：致命冲突，必须消除
- `P1`：高风险，默认自动补强
- `P2`：优化项，仅在不增加无谓复杂度时处理

默认使用 **Silent Preflight**，内部完成检查，不向用户输出冗长报告。

当用户要求分析、诊断、检查或“为什么生成失败”时，使用 **Visible Preflight** 展示真正影响结果的风险。

### Postflight

Postflight 不重新设计用户需求，只检查最终 Prompt 是否重新引入：

- 人物数量或身份冲突
- 素材职责污染
- 时间轴断裂
- 运镜冲突
- 左右手、人物位置或道具状态矛盾
- 声音、对白、字幕职责冲突
- 不必要的负向堆叠

## 核心差异

### Seedance 2.0

更适合“告诉模型你想拍什么，让模型参与导演”。

支持三种提示词组织方式：

- 自由叙事型
- 镜头编号型
- 时间轴型

不为了形式强制时间轴。

### Seedance 2.5

更适合“用户自己当导演，让模型按方案执行”。

默认强化：

- 多模态素材职责映射
- 连续秒级时间轴
- 多角色与声音映射
- 镜头 / 动作 / 声音分层控制
- 故事板、参考视频、白模、局部重拍等工作流

## 使用方式

把仓库作为 Skill 使用后，可以直接描述：

```text
使用 $xiaoyunque-seedance-prompt-director。
小云雀 Seedance 2.5，6 秒，两镜头。
图1是场景，图2是科学家，图3是机器人。
机器人摘下柿子后通过遮挡转场进入实验室，把同一颗柿子递给科学家。
无BGM，只保留环境音、动作音和对白。
只要最终提示词。
```

Skill 会先静默执行 Preflight，再编译 Prompt，并在输出前执行 Postflight。

也可以把已有 Prompt 或生成失败案例发给它，要求分析问题并重新编译。

## Reference 职责

主 `SKILL.md` 只负责工作流、模型路由、输出模式和 reference 路由；具体规则放在独立 reference 中，避免重复维护。

```text
.
├── SKILL.md
├── README.md
├── agents/
│   └── openai.yaml
└── references/
    ├── preflight-compiler.md
    ├── seedance-2.0.md
    ├── seedance-2.5.md
    ├── asset-mapping.md
    └── audio-and-dialogue.md
```

- `preflight-compiler.md`：生成前校验、风险分级、规范化与 Prompt Density
- `seedance-2.0.md`：Seedance 2.0 平台适配与结构选择
- `seedance-2.5.md`：Seedance 2.5 平台适配、时间轴和多模态控制
- `asset-mapping.md`：参考素材职责隔离与角色绑定
- `audio-and-dialogue.md`：BGM、环境音、拟音、对白、VO、字幕与混音

后续如果建立生成失败知识库，放入 `references/troubleshooting.md`，与 Preflight 分开维护。

## 资料依据

本 Skill 当前主要根据：

1. Seedance 2.0 官方发布资料
2. Seedance 2.5 官方发布资料
3. 小云雀 Seedance 2.0 使用手册
4. 小云雀 Seedance 2.5 使用手册
5. 小云雀课程与工程案例的实际提示词写法
6. 实际生成失败案例沉淀出的经验性风险规则

经验规则与官方规范保持区分，不把不可验证的工程经验包装成官方限制。

后续会继续根据真实工程案例迭代规则。
