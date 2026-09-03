# Agent Note: 收起的 Turn-process 成员不占列高度

Status: implemented

[English](2026-09-03-turn-process-until-found-flex-collapse.md) | 中文

## 问题

Turn 在 Compact 模式关闭后，过程控件会汇总工具调用和消息次数，但点击并不会隐藏或显示过程行。这些行在收起时仍按完整高度绘制，展开只补上 `:not([hidden])` 间距原先省略的 16px 行距，因此披露控件读起来像是摘要下方凭空变高。

## 决策

关闭的过程 Seat 仍使用 `hidden="until-found"`，以便在支持的浏览器中进行页内查找并接收 `beforematch`。Chat 列上 `.column > .flowItem[data-turn-process-hidden]` 与 `.column > .flowItem[hidden="until-found"]` 的作者 CSS 同时强制 `content-visibility: hidden`、size containment、`min-height: 0`、`height: 0`、`overflow: hidden` 和 `visibility: hidden`。同一裁剪也作用于 `.body > [data-turn-process-inline][hidden]`，使最终 Step 的推理在答案 Seat 内收起。成员资格、持久化与控件文案仍由 [Web Turn process 折叠](../feature/2026-08-14-web-turn-process-folding.zh.md) 拥有。

列向 flex item 默认 `min-height: auto`。引擎若解析 `hidden="until-found"` 却不施加 size containment，就会保留内容最小高度、绘制子树，并且仍匹配 `[hidden]`，因此兄弟间距保持关闭。除属性外还必须有作者裁剪；UA stylesheet 不足以约束这条 flex 列。

## 曾考虑的替代方案

**只依赖 `hidden="until-found"` 的 UA stylesheet。** 否决，因为 Chat 列是 `display: flex; flex-direction: column`，缺少 size containment 时该属性不会收起这些 item。

**把 `until-found` 换成布尔 `hidden` 或 `display: none`。** 否决，因为支持该能力的浏览器会失去对收起过程文本的页内查找恢复，而这仍是折叠决策的要求。

**把 Chat 列从 flex 改成 block。** 否决，因为该列是未来虚拟化器预留的测量单位；裁剪只作用于关闭的 Seat，不改变可见行布局。

## 后果

Compact 模式下收起的过程行不占列高度；展开后恢复普通的 16px 节奏，并绘制已经挂载的 renderer。页内查找仍取决于是否支持 `hidden="until-found"`。单元覆盖固定关闭成员带有 `data-turn-process-hidden`，以及打开控件时移除该属性。
