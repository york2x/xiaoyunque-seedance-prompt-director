---
name: xiaoyunque-seedance-prompt-director
description: Generate, optimize, preflight, diagnose, and audit prompts for Xiaoyunque Seedance 2.0 and 2.5. Routes model-specific prompt structures, maps image/video/audio references, validates conflicts before generation, plans timelines, directs camera/action/sound, and checks continuity after compilation.
---

# 小云雀 Seedance 2.0 / 2.5 提示词导演

用于小云雀平台 Seedance 2.0 与 Seedance 2.5 视频提示词的生成、改写、前置校验、诊断、审核与压缩。

## 核心原则

1. 先锁定用户已明确的角色、场景、时长、构图、镜头和参考素材职责。
2. 明确“谁参考谁、参考什么、不参考什么”。
3. 在生成 Prompt 前先执行 Preflight，优先消除致命冲突和高风险歧义。
4. 让镜头、动作、声音各司其职，避免互相冲突。
5. 声音事件尽量绑定可见动作或剧情节点。
6. 负向约束只兜底，不堆无关“不要”。
7. 输出以可直接粘贴进小云雀的 Prompt 为主。

## 总体工作流

默认执行：

`User Brief → Preflight → Prompt Compiler → Postflight → Final Prompt`

职责固定为：

- **Preflight validates the brief.** 检查用户需求、素材映射与结构是否可编译。
- **Prompt Compiler** 将通过校验的信息组织成适合 Seedance 2.0 / 2.5 的最终提示词。
- **Postflight validates the compiled prompt.** 检查最终 Prompt 是否重新引入人数、时序、镜头、连续性、声音或素材职责冲突。

Preflight 完整规则读取：`references/preflight-compiler.md`。

## 模型路由

### Seedance 2.0

默认理解为“告诉模型想拍什么，让模型参与导演”。

更适合：
- 5–15 秒短片
- 少量图片参考
- 简单剧情、广告、蒙太奇、美食、短对白

可使用：自由叙事型、镜头编号型、时间轴型。不要为了形式强制时间轴。

完整规则读取：`references/seedance-2.0.md`。

### Seedance 2.5

默认理解为“用户自己当导演，让模型按方案执行”。

更适合：
- 4–30 秒单次生成
- 多图片 / 视频 / 音频参考
- 多角色、多场景、长叙事
- 秒级时间轴
- 故事板、白模、视频编辑、片段重拍、视频延长

默认优先连续时间轴。

完整规则读取：`references/seedance-2.5.md`。

如果用户明确指定模型，不擅自更换模型；只有在导演模式下提示明显不匹配风险。

## 任务模式

根据请求识别：

1. text-to-video
2. image-reference
3. multi-reference
4. first-last-frame
5. reference-video / remake
6. video-edit / segment-retake
7. video-extension
8. storyboard
9. white-model / 3D-director

如果用户没说明，按素材与目标推断，不为无关细节追问。

## 素材映射

所有参考素材先建立职责边界：

`素材ID → 主要职责 → 允许参考属性 → 禁止迁移属性`

默认优先“一素材一主要职责”；需要多个次要职责时明确边界。

多人时逐一绑定人物与素材；同一角色多张视角图需要明确属于同一角色。

完整规则读取：`references/asset-mapping.md`。

## 镜头、动作与连续性

每段优先组织为：

`景别/机位 → 主体位置 → 动作链 → 物理反馈 → 运镜 → 转场`

动作必须有明确主体、方向和必要的身体部位 / 接触关系。

自然物理反馈遵循：

`局部受力 → 合理局部反馈 → 稳定`

不要把局部受力扩大成整场景晃动。

多镜头连续性使用：

`上一镜结束状态 == 下一镜开始状态`

重点保持人物身份、服装、体型、左右位置、左右手、道具归属和状态、姿态及声音连续。

## 人物表演

按：

`视线 → 表情/微表情 → 呼吸/姿态 → 动作 → 对白`

多人对白明确谁先说、谁后说、谁不说话；必要时限制未说话人物同步张嘴。

## 声音导演

声音拆成：

1. BGM
2. 环境音
3. 动作拟音
4. 对白 / VO
5. 字幕

优先采用：

`动作发生 → 对应声音发生 → 动作结束 → 声音收束`

对白与字幕独立控制。

完整规则读取：`references/audio-and-dialogue.md`。

## 输出模式

### 快速模式
用户说“简约版 / 只要提示词 / 不要 Markdown”时：
- 执行 Silent Preflight
- 执行 Postflight
- 只输出最终 Prompt

### 标准模式
默认：
- 执行 Silent Preflight
- 执行 Postflight
- 输出可复制 Prompt
- 最多补充少量必要注意事项

### 导演模式
用户要求详细分析、诊断、检查或优化时：
1. 执行 Visible Preflight
2. 只指出真正影响结果的风险
3. 必要时给素材映射、时间轴和声画设计
4. 编译 Prompt
5. 执行 Postflight
6. 输出最终 Prompt

## Preflight

完整规则见 `references/preflight-compiler.md`。

风险等级：
- `P0`：致命冲突，必须消除。
- `P1`：高风险，默认自动补强。
- `P2`：优化项，仅在不会增加不必要复杂度时处理。

默认 Silent Preflight；分析、诊断、检查和导演模式使用 Visible Preflight。

## Postflight / Prompt 审核器

Postflight 不重新设计用户需求，只验证编译结果。

重点检查：
- 模型结构是否匹配 2.0 / 2.5
- 最终人数与角色清单是否一致
- 素材职责是否在编译中被污染
- 时间轴是否连续且符合总时长
- 单段是否过载
- 左右手、人物位置、道具状态是否前后冲突
- 运镜是否互相打架
- 转场是否有触发点
- 动作是否符合物理逻辑
- 声音、对白与字幕职责是否清楚
- 负向指令是否过多或重复
- 真实感要求是否自相矛盾

诊断输出：`问题 → 原因 → 修改策略 → 最终 Prompt`。

## Reference 路由

需要时按职责读取，不把所有 reference 同时加载：

- 前置校验：`references/preflight-compiler.md`
- Seedance 2.0：`references/seedance-2.0.md`
- Seedance 2.5：`references/seedance-2.5.md`
- 素材映射：`references/asset-mapping.md`
- 声音 / 对白 / 字幕：`references/audio-and-dialogue.md`

后续如建立故障知识库，生成结果失败后的诊断规则放入 `references/troubleshooting.md`，不要混入 Preflight。

## 边界

- Preflight 与 Prompt 优化只能降低生成风险，不保证模型 100% 执行。
- 用户提供新官方资料或真实工程案例时，优先更新规则。
- 明确区分“小云雀平台规则”“Seedance 模型能力”和“经验性优化建议”。
- 官方资料没有支持的结论，不包装成官方规范。
