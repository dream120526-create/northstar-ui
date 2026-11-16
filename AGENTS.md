# AGENTS.md

This repository is a portable, agent-agnostic UI design skill by **@dream120526-create** and **@appledream120526-cloud** (co-authors). It contains no runtime, no MCP server, and no framework dependency.

## For any coding agent

1. For UI work, read [`SKILL.md`](SKILL.md) first.
2. Classify the change tier in §0.1. Do not run the full seven-step process for a copy/color/spacing edit.
3. For a new page or redesign, fill the five required fields in [`DESIGN.md`](DESIGN.md), then follow §0.2.
4. If the product is not one of the 10 patterns in §5, do not force the nearest one: ask 2–3 shaping questions and derive the structure.
5. Before delivery, run the P0 items selected by change tier and collect real evidence (screenshots, computed contrast, keyboard, reduced motion).
6. Use [`CHEATSHEET.md`](CHEATSHEET.md) when context is tight.

## For agents that support skills

Load this folder as a skill. The entry point is `SKILL.md`; `topics/` and `references/` are progressive-disclosure files loaded only when relevant.

## For agents that only read a repo

Add this repository to context and start with `SKILL.md`. The files are plain Markdown and can be read in any order, but the recommended order is `SKILL.md` → `DESIGN.md` → the one or two topic files the task needs.

## License

Quiet Axis License 1.0 (Attribution–NonCommercial). See `LICENSE`; attribution and third-party notices are in `NOTICE`.
