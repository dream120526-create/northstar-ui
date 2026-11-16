# Northstar UI

**A portable design skill for any AI coding agent.** It turns "make it look good" into a sequence an agent can execute and verify: classify content → structure the page → build hierarchy → use motion to explain state change.

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [Español](README.es.md)

---

## Why this exists

Most agent-generated UI fails in the same three ways: content is not classified, pages have no regions, and every element has the same visual weight. Northstar UI is a Markdown skill that gives an agent a small set of hard rules, a design contract, and evidence-based gates—without locking it to a framework or a vendor.

It is deliberately **small enough to keep in context** and **specific enough to change output**.

## What you get

| File | Purpose |
| --- | --- |
| `SKILL.md` | The entry point: change tiers, seven steps, defaults, four motion curves, 22 P0 gates |
| `DESIGN.md` | A fill-in design contract: product facts, one-sentence north star, hero per screen, tokens, do/don't |
| `CHEATSHEET.md` | A one-page card for 10-minute work |
| `topics/motion.md` | 6 button interactions + 16 UI effects, with runnable code and SwiftUI/Compose equivalents |
| `topics/patterns.md` | 10 page patterns, data-dense pages, and a method for deriving patterns outside the 10 |
| `topics/recipes.md` | Tokens, shell, buttons, dialog, toast, skeleton, full list/settings/form pages |
| `topics/reference.md` | 48 anti-patterns, measured token contrast, typography, accessibility, review format |
| `topics/i18n.md` | Mixed CJK/Latin, punctuation, numbers, RTL, font stacks |
| `topics/delivery.md` | HTML / IPA / APK / EXE builds, signing, real-device acceptance |
| `references/agent-ui-skills.md` | Methodology comparison of 14 agent UI skills |

Highlights:

- **Not a pattern dump.** There are 10 skeletons, plus a documented way to derive the 11th: list content, ask 2–3 shaping questions, apply the five zones, record it in `DESIGN.md`.
- **AI-slop audit.** 18 signals (purple gradients, four identical KPI cards, emoji icons, fake metrics) with an Audit → Direction → Passes repair order.
- **Motion that matches intent.** Constant speed = `linear`; exit = `ease-in`; enter = `ease-out`; in-page movement = `ease-in-out`; spring only for feedback.
- **Evidence, not claims.** Contrast from real hex, 390/768/1440 screenshots, keyboard run, 280px overflow, reduced-motion pass.
- **Platform-aware, agent-agnostic.** Web, iOS, Android, and desktop shells; works with Claude Code, Codex, Cursor, Gemini CLI, Windsurf, Aider, or a plain LLM.

## Install

Copy the folder into your agent's skills directory, or point the agent at `SKILL.md`.

```bash
git clone https://github.com/dream120526-create/northstar-ui.git
```

Common locations:

| Agent | Path |
| --- | --- |
| Claude Code | `~/.claude/skills/northstar-ui/` or `<project>/.claude/skills/northstar-ui/` |
| Codex | `~/.agents/skills/northstar-ui/` or `<project>/.agents/skills/northstar-ui/` |
| Gemini CLI / Antigravity | `~/.gemini/config/skills/northstar-ui/` |
| Cursor | `<project>/.cursor/skills/northstar-ui/` or add a pointer in `.cursor/rules` |
| Windsurf | `<project>/.windsurf/skills/northstar-ui/` |
| Any other agent | Put the folder anywhere and add one line to `AGENTS.md`: "For UI work, read `northstar-ui/SKILL.md` first." |

No runtime, no MCP server, no build step.

## Use it

```text
Read northstar-ui/SKILL.md. Build the billing settings page.
Start with a DESIGN.md draft, then follow the seven steps. Do not skip classification.
```

For a small edit:

```text
Use northstar-ui. Change the primary button color; check token contrast (P0 9) only.
```

The skill defines change tiers, so an agent does not run a full redesign for a copy edit.

## Design principles

1. **Logic before pixels.** Classification, structure, hierarchy—then visual polish.
2. **One hero and one primary action per screen.** Everything else is demoted.
3. **Motion explains state change.** If removing it loses no meaning, remove it.
4. **No evidence, no pass.** Screenshots, computed contrast, keyboard, reduced motion.
5. **One direction, stated in one sentence.** "Modern and clean" is not a direction.

## Repository structure

```text
northstar-ui/
├── SKILL.md
├── DESIGN.md
├── CHEATSHEET.md
├── LICENSE
├── NOTICE
├── README.md
├── README.zh-CN.md
├── README.ja.md
├── README.es.md
├── topics/
│   ├── motion.md
│   ├── patterns.md
│   ├── forms.md
│   ├── states.md
│   ├── platforms.md
│   ├── i18n.md
│   ├── delivery.md
│   ├── recipes.md
│   └── reference.md
└── references/
    └── agent-ui-skills.md
```

## License and attribution

**Northstar UI License 1.0 (Attribution–NonCommercial).** You may copy, modify, and build on it. You must credit both authors (or link to an original repository), and you may not sell it or charge for access. See [LICENSE](LICENSE) for the full terms.

Created by [@dream120526-create](https://github.com/dream120526-create) and [@appledream120526-cloud](https://github.com/appledream120526-cloud).

Third-party methodology references and CC BY 4.0 attribution: [NOTICE](NOTICE).
