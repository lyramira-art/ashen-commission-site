# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static marketing site for **Msz / "Mszook / Ashen"**, an independent music-commission
artist (VOCALOID P主, ACG/二次元 角色曲·印象曲·企划曲). It is a personal *宣传主场*
(value-persuasion page) — **not** a marketplace/接单平台. Ships as plain static HTML:
**no build system, no framework, no tests, no package.json.**

- Remote: `git@github.com:lyramira-art/ashen-commission-site.git`
- Deploy: **GitHub Pages (origin, from `master` root) fronted by Tencent EdgeOne for China access.**
  Full runbook + China caveats (ICP 备案, fonts) in **`DEPLOY.md`**.

## Files

- **`index.html`** — the production site. Self-contained: inline styles + one vanilla `<script>`,
  no dependencies. Palette *parchment + gold + indigo* (`--parch:#f3e8d1`, `--gold:#b8892f`,
  `--indigo:#322c63`; fonts Cinzel / Noto Serif SC / Shippori Mincho). Sections: Hero · 能帮你什么(help) ·
  作品试听(works) · 曲师介绍(about) · 流程(process) · FAQ · 联系(contact) + 委托 modal.
- **`uploads/`** — images. **`audio/`** — preview clips (see `audio/README.md`).
- **`prototype-v1.html`** — superseded v1 prototype (dark charcoal/ember). Kept for reference only.
- `README.md` / `02-service-menu-v1.md` / `03-prd-v1.md` — **internal strategy, NOT site content**
  (client battle-map, service menu 退居内部参考, PRD text spec).

## `index.html` was flattened from a Claude Design canvas — how to re-edit

The **design source of truth** is an external Claude Design *canvas* file:
`~/Downloads/独立音乐人个人委托主场/Mszook 委托主场 v3.dc.html` (+ its `support.js` / `image-slot.js`
runtime). `index.html` is that canvas **flattened** for static hosting.

- **Small tweaks** (copy, colors, a new work card): edit `index.html` directly. It's the deployed artifact.
- **Big redesigns**: do them in Claude Design on the `.dc.html`, then re-flatten. Flattening =
  unwrap `<x-dc>`/`<helmet>`, drop `support.js`/`image-slot.js`, convert the
  `<script type="text/x-dc" data-dc-script>` (a `DCLogic` component) into a plain vanilla `<script>`,
  and replace every `<image-slot>` with a real `<img>`. **Do NOT ship the raw `.dc.html`** — it
  depends on the design-tool runtime (injects React, needs the editor host bridge) and renders dead
  without it. Keep asset paths **relative** (`uploads/…`, `audio/…`) so they survive the Pages
  subpath / CDN / subdomain.

## How the interactive bits work (all in the one `<script>`)

- A `PROPS` object at the top of the script sets defaults: `theme` (羊皮纸/陈年卷轴/星夜),
  `commissionStatus` (接稿中/限时开放/暂停接稿 → drives the hero status pill), `showRunes`.
- **Theme**: 3 palettes live in `_applyTheme`; the floating ◐ button toggles light(羊皮纸)/dark(星夜).
- **Audio = progressive enhancement.** Each `[data-track]` card may carry `data-audio="audio/xxx.mp3"`.
  If the file loads → **real playback** (progress/seek/time from the `<audio>` element). If absent /
  404 / unsupported → it **degrades to a silent "demo" progress bar** (`requestAnimationFrame` over
  `data-duration`). Only one track plays at a time. Add a work card by copying a `data-track` block —
  the player auto-binds it, no JS change needed.
- **Images degrade too**: covers/avatar/QR are `<img onerror="this.remove()">` layered over a ✦/text
  placeholder, so missing files look intentional, not broken. Expected filenames: `DEPLOY.md`.

## Content rules (from the PRD — these constrain all copy)

- **说人话** — plain language; collapse工种术语 (作曲/编曲/混音/母带) into one line. Audience = non-musician clients.
- **全程不提 AI** — never mention AI anywhere in site content. Non-negotiable.
- **音频优先** — audio is the strongest proof; every work gets one line of语境 (为谁写 / 什么角色情绪 / 亮点).
- Dark, author-ish aesthetic is itself part of the pitch.

Open content TODOs (copy dupes, placeholder links, contact accounts, oversized banner) are tracked
in `DEPLOY.md` → 待办清单.

## Local preview

```
python3 -m http.server 8000     # then visit http://localhost:8000/
```
(The raw `*.dc.html` won't fully work this way — it needs the dc-runtime. Preview `index.html`.)
