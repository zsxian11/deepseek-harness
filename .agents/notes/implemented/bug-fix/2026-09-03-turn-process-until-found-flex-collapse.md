# Agent Note: Collapsed Turn-process members occupy no column height

Status: implemented

English | [中文](2026-09-03-turn-process-until-found-flex-collapse.zh.md)

## Problem

After a Turn closes in Compact mode, the process control summarizes Tool calls and messages, but clicking it does not hide or reveal the process rows. Those rows stay painted at full height while collapsed, and expansion only adds the 16px sibling gaps that `:not([hidden])` spacing had omitted, so the disclosure reads as empty growth under the summary.

## Decision

Closed process Seats keep `hidden="until-found"` for find-in-page and `beforematch` in supporting browsers. Author CSS on `.column > .flowItem[data-turn-process-hidden]` and `.column > .flowItem[hidden="until-found"]` also forces `content-visibility: hidden`, size containment, `min-height: 0`, `height: 0`, `overflow: hidden`, and `visibility: hidden`. The same clip applies to `.body > [data-turn-process-inline][hidden]` so final-Step reasoning collapses inside the answer Seat. Membership, persistence, and control labeling remain owned by [Web Turn process folding](../feature/2026-08-14-web-turn-process-folding.md).

A column flex item defaults to `min-height: auto`. Engines that parse `hidden="until-found"` without applying size containment therefore keep the content minimum, paint the subtree, and still match `[hidden]` so sibling gaps stay off. Author clipping is required in addition to the attribute; the UA stylesheet is not sufficient on this flex column.

## Alternatives considered

**Rely on the `hidden="until-found"` UA stylesheet alone.** Rejected because the Chat column is `display: flex; flex-direction: column`, and the attribute does not collapse those items when size containment is missing.

**Replace `until-found` with boolean `hidden` or `display: none`.** Rejected because supporting browsers would lose find-in-page recovery of closed process text, which the folding decision still requires.

**Change the Chat column from flex to block.** Rejected because the column is the reserved measurement unit for a future virtualizer; the clip is local to closed Seats and leaves visible row layout unchanged.

## Consequences

Collapsed process rows occupy no column height in Compact mode; expanding them restores the ordinary 16px rhythm and paints the existing mounted renderers. Find-in-page still depends on `hidden="until-found"` support. Unit coverage pins `data-turn-process-hidden` on closed members and its removal when the control is open.
