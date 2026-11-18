---
name: product-prd-workflow
description: 产品管理的统一入口，用于编写或更新 PRD、制作可点击的 HTML 原型与流程图、编写功能验收清单，支持单项交付和整套交付。也用于整理用户反馈与复盘指标。
---

# 产品需求文档工作流

从当前 `SKILL.md` 所在目录定位随技能安装的 `assets/` 和 `scripts/`。将产物写入用户指定的项目，缺省使用当前工作区。如果项目已有对应的 `.agents/workflows/` 文件，优先用项目版，否则读下表对应的工作流。按任务使用 Codex 可用的文件读取、搜索和数据工具。

用户指定 Markdown 或纯文本 PRD 时，使用 `kw-product:write-spec`（可用时）整理需求并直接交付所需格式。HTML 路由适用于 HTML PRD、原型与流程图。

| 用户需要 | 工作流 | 默认产物 |
|---|---|---|
| 新写或修改 PRD、交互原型、流程图 | [PRD](assets/workflows/edu-pm-prd.md) | HTML PRD，按需配原型和图 |
| 用户反馈整理、需求洞察 | [需求挖掘](assets/workflows/edu-pm-demand.md) | 需求洞察报告 |
| 功能验收、上线前产品走查 | [验收](assets/workflows/edu-pm-acceptance.md) | 验收清单 |
| 业务指标、漏斗、上线复盘 | [数据分析](assets/workflows/edu-pm-data-analysis.md) | 数据分析报告 |

按用户要求交付。只做原型或验收清单时，优先用用户提供的 PRD 或当前项目的同名需求文档。缺少影响功能、权限或验收结果的关键信息时，提出聚焦的问题。

需要同时交付 PRD、原型和验收清单时，先明确需求与验收口径，再用同一份 PRD 驱动原型和清单。三份产物放在同一个需求目录。交付前对齐需求编号、页面状态、业务规则与预期结果，引用实际交付的文件。

工作流中的模拟反馈、章节确认和产物默认值按本入口所述的适用条件执行。同名需求已有文件夹时复用，按需创建 `需求文档/`、`原型/`、`流程图/`、`需求挖掘/`、`验收清单/` 或 `数据分析/`。HTML PRD 从[正文模板](assets/templates/prd-content.html)开始，交付前用[格式校验器](assets/scripts/validate_prd.py)检查，并核对占位符、业务规则与资源引用。项目已有对应模板或校验器时使用项目版。

撰写文档与静态预览可直接使用随技能安装的资源。需要截图导出、复制时内嵌截图或浏览器写回时，复用项目中的导出服务。若需要部署项目资源，用 `python3 <技能目录>/scripts/initialize.py --project <目标目录> --no-launcher` 初始化（Windows 用 `py -3`）。在目标目录用 `python3 scripts/start_service.py --serve` 启动本地导出服务。用户要求 macOS 按需自动启动时可启用 LaunchAgent。工作流中提到的旧技能路径，统一从当前 `SKILL.md` 所在目录定位。

## 搭配已安装的 Codex 插件

本次任务需要且对应技能可用时，将以下方法融入同一份交付物。

- 输入包含访谈、问卷、工单或用户反馈时，用 `kw-product:synthesize-research` 提炼主题及证据。缺少真实材料时，将潜在痛点写成待验证假设。用户要求演示时，可以使用明确标注的模拟数据。
- 写新需求或调整范围时，用 `kw-product:write-spec` 明确用户问题、范围、优先级与可测试的验收标准。把验收标准写在对应的需求或功能下。
- PRD 引用数据或包含指标目标时，用 `kw-data:validate-data` 核对可取得的数据来源、分母、统计窗口和基线，标记待确认口径。
- 做功能验收或上线回归时，用 `kw-engineering:testing-strategy` 确定核心路径、异常状态、权限边界和预期结果。

## HTML PRD 编写规则

- **Confirm the requirement before writing when core information is missing; ask only focused questions.** The six-dimension completeness check has a **材料** dimension (§1.2): ask once for the tracking dictionary, design spec / brand accent, and any existing prototype screenshots — finding out at §数据埋点 that no dictionary exists means interrupting the user mid-draft or inventing field names.
- **Carry the confirmation into the document.** §1.4 maps each confirmed item to its PRD location: 核心改动 → 功能清单+详细方案; 成功指标 → 需求目标表; **不做 / 范围外 → 需求概述的「范围块」**; 背景证据 → 需求背景三段式; 跨页规则 → 需求概述规则表; 待确认项 → 正文末尾清单. A "不做" that was agreed verbally but never written into the PRD does not count as confirmed.
- **Copy `assets/templates/prd-content.html` and fill it in — do not hand-write the HTML skeleton.** That file already carries the right class names, table headers, `colgroup` widths, `.desc-block` structure and `data-preview` placeholders; nearly every historical defect (descriptions using `<br>` instead of `<p>`, wrong column counts, missing `colgroup`) came from rebuilding the structure from memory.
- **Run `assets/scripts/validate_prd.py` before delivery and report the result honestly.** It mechanically checks: core chapters present, 项目信息/版本记录 not merged, no standalone boundary chapter, continuous chapter numbering, all six table headers and column counts, meta-table is two-column, every 详细方案 row has `data-preview` and a non-empty 原型 cell, description cells use `.desc-block` with no `<br>` and no multi-number cramming, snake_case tracking names with six columns, `#prdContent` contains its own `<style>`, and the goal table header. A pass does NOT mean the document is good (visual and editorial quality still need human review) — but a failure means it is definitely not done.
- **Fixed chapter order, tailored to the requirement's size — do not force all sections.** Order is 项目信息 → 版本记录 → 需求背景 → 需求目标 → 需求概述 → 业务流程图 → 交互流程图 → 详细方案 → 数据埋点 → 时序图（上线计划/附录 only if genuinely needed）. Core skeleton always kept: 项目信息, 版本记录 (two separate tables — never merged), 需求背景, 需求目标, 详细方案. The other seven (需求概述/业务流程图/交互流程图/数据埋点/时序图/上线计划/附录) are dropped when the requirement doesn't need them. Tailor optional sections to the requested deliverables and confirm consequential scope decisions when the request leaves them open. If 业务流程图 is dropped, skip producing the flowchart file; if 交互流程图 is dropped, skip the screenflow file. See edu-pm-prd.md §2.2 and §1.3.1.
- **No standalone 异常/边界 chapter.** Boundary cases live in the owning row's 【边界说明】 block inside 详细方案 (§2.3.1), never as a document-level chapter.
- **需求目标 states the user outcome and how it is measured, not implementation actions.** Quantifiable goals go in a 用户结果/衡量指标/统计口径/预期方向/目标值 table; with no real baseline write 「待基线确认」 — never invent a percentage. Non-quantifiable goals must still be observable and verifiable. Same rule for numbers cited in 需求背景: give the source or mark it unconfirmed. See §2.2 ④.
- **The 原型 column accepts a high-res screenshot (`img.proto-shot` with real width/height, optionally linked to the clickable prototype) or an interactive iframe — do not convert existing screenshots into iframes.** See §2.3 and §3.3.
- **详细方案 description cells: two mandatory blocks, the rest by a written test — not by habit** (§2.3.1). 【页面元素】and【交互说明】are mandatory in every cell. For any optional block the test is: **"if this page were deleted, would the rule still hold?"** If yes, it is a cross-page rule and belongs in the 需求概述 rule table (§2.3.1 ③), not repeated in each cell — 签到 1/3/7-day reward tiers, cumulative score/quota accounting, grant timing, role-permission matrices and global rate limits are the usual offenders. If no, it belongs to this page. Typical symptoms of over-writing: the same shared rule copy-pasted into three cells, or four-plus blocks in one cell. 【功能逻辑】 in particular should carry only rules that stop being true when this page is gone. Also: never write "同上" / "详见 x.x" — a row gets copied into review documents on its own; never pad with "无"; every item is its own `<p>`/`<li>` inside a `.desc-block`; multiple numbered items crammed into one paragraph is a defect. Absolutely no technical API/interface wording in description cells.
- **One row = one screen or one state variant** (§2.2 ⑦). A 二级功能 spanning three screens becomes three rows, each with its own prototype asset; a screen's multiple states stay in one row and are enumerated in 【页面元素】. If you cannot name the single image the 原型 cell shows, the granularity is wrong.
- **需求概述 is more than a feature list** (§2.2 ⑤): it carries a **范围块** (本期范围 / 本期不做什么·暂不展开) plus the feature-list table plus, when needed, the cross-page rule tables (`h3` + small table). If the whole chapter is dropped for a small requirement, the 范围块 and rule tables must relocate — 功能清单 may go, but "what we are NOT doing" and the shared rules may not.
- **需求背景 is three parts** (§2.2 ⑥): who hits what problem in what scenario → what impact it has → what evidence supports it (state plainly when there is none; never invent). **需求目标** closes with a 「待后续确认的产品口径」 list collecting every 待确认 in the document, each with owner and impact.
- **The edit module ships undo/redo** (§2.7 ⑤): ⌘Z / Ctrl+Z undo, ⌘⇧Z / Ctrl+Y (also Ctrl+⇧Z) redo, covering text edits, row add/delete, table delete and column/row resize on one stack; a new edit clears the redo branch. Deleted rows/tables are kept alive as **DOM nodes** in the stack (not HTML strings) so restored controls still work, and the native contenteditable undo must be `preventDefault`ed in the capture phase or both stacks fire at once. History is memory-only — never promise it survives a refresh.
- **时序图 (sequence diagram)** is an optional dev/QA-facing PRD chapter placed AFTER 数据埋点 (the last body chapter): copyable **Mermaid source-code text blocks — never rendered images or iframes** (devs/QA reuse the text directly; a rendered picture was rejected). Contains an end-side orchestration `sequenceDiagram` (real participants, e.g. 用户/端/Cocos/服务端) plus a key-object `stateDiagram-v2`, each block with a "复制源码" button and 说明 bullets. Technical field/interface names ARE allowed in this chapter (the §2.3.1 ban only covers 详细方案 description cells). See edu-pm-prd.md §4.7.
- **交互流程图 (screen flow)** is an optional PRD chapter placed before 详细方案: one whole image-style HTML canvas stitching all core screens (scaled live prototype iframes) with labeled **right-angle (Manhattan-routed) arrows** for navigation — never bezier curves or diagonal lines; same-side loop-back edges run on offset rails — plus an explicit state-machine legend. Produce per edu-pm-prd.md §4.6; export via the local screenshot service.
- Keep PRD, prototype, and flowchart synchronized. When one changes, inspect the other two for necessary updates.
- The HTML prototype is the primary interactive artifact. Pencil is optional visual enhancement only.
- Use the local PNG export service through `scripts/prototype-export-client.js`; do not build new `html2canvas`/`html-to-image` exporters.
- **「一键复制全文」must turn prototype iframes into inline base64 images** via `POST /api/snapshot` on that same local service (cache-first, auto re-render on stale) — never `iframe.remove()`, which silently drops every prototype from the pasted document. Under `file://` the browser blocks both local `fetch` and canvas readback, so base64 can only come from the service; do not add a front-end screenshot library for this. Degrade in three explicit tiers and always tell the user what was lost. See edu-pm-prd.md §2.8.
- **When pasted images look blurry, raise `MAX_IMG_WIDTH` — do not touch the encoder.** Measured on real assets: JPEG .85, JPEG .95, lossless PNG and WebP .92 are indistinguishable once all four are downsampled to 1200px; the detail is lost in the resize, not the compression. Ship 2000px at quality 0.9. Verify by zooming a pasted image past 1000px — at the default in-document width (≈335px, fixed to the table's 原型 column) every resolution looks identical, so "looks fine pasted" proves nothing. Report clipboard size as the base64 payload (1.33× the decoded bytes), not the decoded bytes. See edu-pm-prd.md §2.8 ⑤.
- **`原型截图/` is the PM's asset folder, not the tool's scratch folder.** When an export run clears "last round's output", delete only the filenames this HTML registered in `.export-manifest.json` — never glob `*.png`. That glob has already wiped 8 hand-made screenshots (captured state-by-state from `[需求名]-shots.html` and hand-named, referenced directly by the PRD's 原型 column) during an export of a `-prototype.html` that produced a single image. Those PNGs are not in git. See edu-pm-prd.md §4.4 item 6.
- **The dual-pane preview is CLOSED by default** (§2.6). The PRD opens as plain readable prose; the panel appears only when the user clicks 双栏预览 (the toggle label flips to 收起预览), and the iframe `src` stays empty until first open so nothing loads in the background. A non-empty prototype folder is NOT a reason to auto-open. The only automatic open is an explicit `#preview=<page-id>` deep link.
- **The dual-pane preview must ship zoom controls** (`－ / % / ＋ / 适配`, same control group as §4.6), keeping `previewZoom` in a JS variable only — never written into the DOM, so saved/copied output stays clean. See edu-pm-prd.md §2.6 ⑨.
- **Business flowcharts must NOT use Mermaid's default theme** (§4.1). `theme:'default'` produces saturated blocks with white text that clash with the light PRD shell. Use `theme:'base'` with the fixed `themeVariables` block (near-white node fill `#faf9ff`, thin `#cbc4e7` border, dark `#30285b` text, `lineColor #8d82b1`), `flowchart.curve:'linear'` for right-angle connectors, `nodeSpacing:60`/`rankSpacing:70` as minimums, and the shared page shell (`#f8f8fc` body, white 14px-radius card). Distinguish node types by shape, not fill colour.
- **Zero text overlap is a hard gate for both diagrams, not a best effort** (§4.3 ②, §4.6 ③④). Fix order when the audit fails: enlarge the canvas → widen gaps → reroute/add rails → split into two canvases. Never fix overlap by shrinking type, hiding a label, deleting the offending branch, or using a background colour as a cover-up. Deliver only after the geometry audit returns `ok:true` AND you have looked at the diagram at 100% zoom both standalone and inside the PRD iframe — a diagram that is clean at 60% can still be overlapping when embedded. See §4.6 ④ for the audit snippet and the export-time guard that blocks shipping an overlapping diagram.
- **Anything the PRD does over `http://` must survive an insecure context.** On the intranet `navigator.clipboard`, `ClipboardItem` and `showSaveFilePicker` do not exist: copy paths need an `execCommand` fallback and save degrades to downloading a copy. The export service must resolve the calling page by project-relative path too, or `/api/snapshot` and `/api/asset` return 400 and 一键复制全文 silently loses every image. See edu-pm-prd.md §2.9.
- Before delivering an HTML PRD, verify rendering and references, check alignment with the prototype pages, and run `validate_prd.py`.

## Optional Pencil Path

Offer Pencil enhancement only after the HTML prototype is usable, or when the user asks for high-fidelity design. If Pencil is used:

- Confirm Pencil is running and the MCP connection is available.
- Keep HTML and Pencil color/spacing decisions synchronized.
- Use Pencil screenshots as visual validation, but keep the HTML prototype as the PRD iframe source.
