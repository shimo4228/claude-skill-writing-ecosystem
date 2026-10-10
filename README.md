# claude-skill-writing-ecosystem

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/claude-skill-writing-ecosystem)

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) and six review subagents for writing **human-facing** articles, essays, blog posts and newsletter issues with Claude Code. The `writing-ecosystem` skill takes one piece from a single central thesis, agreed with you before any outline, through drafting and a reviewer panel to your approval of the body (content GO), title review and acceptance. You decide the claim and the final call; Claude drafts and reviews.

AI-facing documents (`llms.txt`, FAQ pages, glossaries) belong to the sibling skill [llms-txt-writer](https://github.com/shimo4228/llms-txt-writer) instead. Articles on how this writing flow came to be, and the author's other repos, are listed under [More from the author](#more-from-the-author).

## Before you start

- **Claude Code.** The skill and agents are Markdown only; there are no runtime dependencies. SKILL.md is written in Japanese with English technical terms, and the agent definitions mix Japanese and English.
- **A publication channel contract in your project.** A rules file under `<project>/.claude/rules/` with one row per channel, the place a piece is published (the author's rows are Zenn and note in Japanese, Dev.to and Substack in English), giving its path, reader, register, reviewer panel, title limits and publish handoff. The skill resolves the file you are writing to one channel and stops rather than guess when the path matches no channel or more than one. The author's own contract is public as an example: [publishing-channels.md](https://github.com/shimo4228/zenn-content/blob/main/.claude/rules/publishing-channels.md).
- **A writing-principles file.** SKILL.md tells Claude to read the author's writing backbone at `~/MyAI_Lab/zenn-content/.claude/rules/writing-principles.md` before writing; that path exists only on the author's machine. Point that line of your installed SKILL.md at your own file, or put your principles in your project's `.claude/rules/` (rules there load on their own); you can start from the author's [writing-principles.md](https://github.com/shimo4228/zenn-content/blob/main/.claude/rules/writing-principles.md).
- **Skills it names but does not bundle (optional routes):** `session-theme-mining` (theme discovery), `collect-context` (material collection), `headline-craft` (title candidates), `quality-gate` (acceptance), `prose-translation` (translation). The skill hands a step to one of them when you take that route; none is needed to go from the brief to your content GO. `quality-gate` and `session-theme-mining` are public in [zenn-content's `.claude/skills/`](https://github.com/shimo4228/zenn-content/tree/main/.claude/skills); `collect-context`, `headline-craft` and `prose-translation` are in the author's [claude-harness](https://github.com/shimo4228/claude-harness/tree/main/skills).
- **What leaves your machine.** `fact-checker` and `theme-reviewer` search the web. The first read is meant to run on a different model family through the [OpenAI Codex plugin for Claude Code](https://github.com/openai/codex-plugin-cc), which needs a ChatGPT account or an OpenAI API key and sends the draft to OpenAI; without the plugin, the bundled `prose-clarity-reviewer` does that read inside Claude Code, on Claude's own model family.

## Install

This repo bundles **both the skill and the agents it orchestrates** (`editor` / `essay-reviewer` / `prose-clarity-reviewer` / `theme-reviewer` / `title-reviewer` / `fact-checker`). The agents read the skill at `<project>/.claude/skills/writing-ecosystem/` and the channel contract at `<project>/.claude/rules/`, so install both into the project you write in.

The skill and agents are in daily use in the author's article repository ([zenn-content](https://github.com/shimo4228/zenn-content), under `.claude/`) and are copied here one way, so this copy can trail that one between syncs.

### Option A: one command (recommended)

```bash
git clone https://github.com/shimo4228/claude-skill-writing-ecosystem
cd claude-skill-writing-ecosystem
CLAUDE_HOME=/path/to/your-project/.claude ./install.sh
```

Copies `skills/*` into `<CLAUDE_HOME>/skills/` and `agents/*.md` into `<CLAUDE_HOME>/agents/`. Always set `CLAUDE_HOME` to your project's `.claude`: without it, the target is `~/.claude`, which leaves the skill outside `<project>/.claude/skills/writing-ecosystem/`, where the agents look for it. Existing files that differ are moved to `<CLAUDE_HOME>/backups/install-<timestamp>/` first (use `--force` to skip backups, `--dry-run` to preview).

### Option B: manual

The same copy by hand, fresh install only, no backups (to update, use Option A: `cp -r` into an existing `writing-ecosystem/` nests a copy):

```bash
mkdir -p /path/to/your-project/.claude/skills /path/to/your-project/.claude/agents
cp -r skills/writing-ecosystem /path/to/your-project/.claude/skills/writing-ecosystem
cp agents/*.md /path/to/your-project/.claude/agents/
```

At v0.2.0 (2026-06-08) this README also listed an install through SkillsMP, a third-party skill directory. That route copied `skills/` only, and as of 2026-10-10 SkillsMP's site says it does not install skills, so use Option A or B.

### First run

In Claude Code, inside the project that holds your channel contract, ask for a new piece ("write an article on …") or run `/writing-ecosystem`. The skill first asks what you are stuck on or want to question, says it back as a thesis, then writes an editorial brief to `docs/plans/<slug>.md` and stops for your confirmation before any outline.

## Ecosystem map

Who owns what, and when each runs. Which channel routes to `editor` or `essay-reviewer` is decided by the channel table in the project's publication channel contract (`<project>/.claude/rules/*.md`), not by article type.

| Phase | Component | Owns | Trigger |
|---|---|---|---|
| **Theme review** | `theme-reviewer` agent | findings on a chosen question; no verdict | when the piece claims novelty against outside discussion and you ask for it |
| **Write** | `writing-ecosystem` skill (this one) | central thesis, causal spine (the order the argument moves in: observation, tension, mechanism, your decision), evidence selection, structure | first draft, full revision; ends at structural freeze, when the skill stops changing the draft's structure and hands the frozen draft to the panel |
| **Review: channel** | `editor` / `essay-reviewer` agent | argument flow, explanation quality, AI slop (generic AI-sounding prose), terminology / logic, overload, tone | after structural freeze |
| **Review: first read** | Codex plugin's `codex:codex-rescue`, reading with the `prose-clarity-reviewer` checklist (the agent itself when Codex is unavailable) | first screen, terminology first use, insider context, category swaps | after structural freeze |
| **Review: facts** | `fact-checker` agent | web verification of factual claims; local check of code, paths and output | before your read-through |
| **Title review** | `title-reviewer` agent | title-body contract; findings only | after your content GO |
| **Acceptance** | `quality-gate` skill (not bundled) | collects the reviewer reports and checks the contract requires; PASS / FAIL / BLOCKED | before your publication GO (the decision to publish; content GO only approves the body) |

## Key contributions

- **One central thesis, agreed before writing.** Your words go verbatim into an editorial brief (reader, central thesis, causal spine, selected evidence, out of scope) before any outline exists.
- **A disposal rule for review findings.** Each panel reviewer reads the frozen draft once; the orchestrator (the `writing-ecosystem` skill) logs adopt or reject, with a reason, per finding; your read-through, not another review round, confirms the fixes. In the author's own runs, each re-review of fixed text returned new findings and piled qualifiers into the prose; the same loop in code review is traced in [this article](https://dev.to/shimo4228/i-cut-my-ai-review-chain-from-6-stages-to-1-breaking-the-loop-that-never-hits-zero-findings-1moi) ([日本語](https://zenn.dev/shimo4228/articles/review-chain-damping)).
- **A first read from another model family.** A reviewer from the writer's own model family skips what the writer skipped, so the `prose-clarity-reviewer` fallback keeps the checklist but not that second view.
- **Title conventions.** Promise nothing the body does not deliver, use only numbers the body measured, and keep the tool names readers search for. Length and notation limits come from the channel contract; apart from those, a candidate is dropped only for honesty, and you pick.
- **AI-slop diagnostics (JA + EN).** [`references/style-diagnostics.md`](skills/writing-ecosystem/references/style-diagnostics.md) pairs generic praise words and structural tells with what to write instead; a diagnostic table, not a string blacklist.

## More from the author

- **[Two Drafts Passed Every Eval and Both Were Hollow. Attach the Raw Transcript to the Ledger You Hand Your AI](https://dev.to/shimo4228/two-drafts-passed-every-eval-and-both-were-hollow-attach-the-raw-transcript-to-the-ledger-you-hand-32p2)** ([日本語](https://zenn.dev/shimo4228/articles/transcript-not-ledger)): two drafts cleared every check and review and were still thrown away; what a tidy evidence ledger drops, and why the raw session transcript goes along with it.
- **[Organic Growth and Content Integrity in an AI Writing Team](https://dev.to/shimo4228/organic-growth-and-content-integrity-in-an-ai-writing-team-1h67)** ([日本語](https://zenn.dev/shimo4228/articles/organic-growth-content-integrity)): the author's AI writing setup two months in (five agents, eleven skills), and the 32 contradictions an audit found between its parts after 42 articles.
- **[zenn-content](https://github.com/shimo4228/zenn-content)**: the Markdown sources of every article and essay (Zenn / Dev.to / note / Substack) with a generated index; its `.claude/` folder is where the writing skill and agents are used daily.
- **[claude-skill-paper-ecosystem](https://github.com/shimo4228/claude-skill-paper-ecosystem)**: an orchestrator skill, a drafting skill and five reviewer subagents for academic papers (SSRN / arXiv / Zenodo), checking each claim against the source it cites.
- **[readme-writer](https://github.com/shimo4228/readme-writer)**: the README counterpart; rewrites or reviews a README so a first-time visitor can tell what the project is, with a fresh judge checking the page against the code.
- **[llms-txt-writer](https://github.com/shimo4228/llms-txt-writer)**: the AI-facing counterpart; writes `llms.txt`, `llms-full.txt`, FAQ and glossary pages.
- **[shimo4228](https://github.com/shimo4228/shimo4228)**: the author's hub, with five long-running projects (each with its own DOI) and the author's tools for Claude Code.

## Acknowledgments

The `article-writing` skill this one grew out of is **not authored by this repo**. It comes from [Everything Claude Code (ECC)](https://github.com/affaan-m/everything-claude-code) by [Affaan Mustafa](https://github.com/affaan-m), MIT-licensed. This skill (`writing-ecosystem`) now carries its own drafting flow, so ECC is no longer required, but the general drafting framework it started from is ECC's contribution.

## License

MIT. See [LICENSE](LICENSE).

<details>
<summary>For tools and AI assistants</summary>

claude-skill-writing-ecosystem is a Claude Code skill bundle (one orchestrator skill, `writing-ecosystem`, and six review subagents) that runs the writing of human-facing articles, essays, blog posts and newsletter issues for writers who use Claude Code ("the writer"), from a central thesis agreed with the writer to the writer's publication decision.

It exists to keep the claim, the structure and the final decisions with the writer while Claude drafts and reviews. The thesis is agreed in conversation before any outline, each reviewer reads the frozen draft once, every finding gets a logged adopt-or-reject decision, and the writer's read-through, not another review round, confirms the fixes, because in shimo4228's own runs each re-review of fixed text returned new findings.

Canonical facts: MIT license; Markdown only (the skill, its references and the agent definitions; SKILL.md in Japanese with English technical terms), with a bash `install.sh`; no runtime dependencies and no paid keys beyond Claude Code itself. Status: active; `skills/` and `agents/` are copied one way from shimo4228's article repository, zenn-content (`.claude/`), where they are used daily, so they can trail it between syncs. Requirements: Claude Code, a publication channel contract under `<project>/.claude/rules/` (the skill stops when the target path matches no channel or more than one), and a writing-principles file; the agents read the skill from `<project>/.claude/skills/writing-ecosystem/`, so the bundle is installed per project with `CLAUDE_HOME=<project>/.claude ./install.sh`. The agents name their Claude model alias in frontmatter: `fable` for editor, essay-reviewer and theme-reviewer, `opus` for prose-clarity-reviewer and title-reviewer, `sonnet` for fact-checker. Optional: the OpenAI Codex plugin for Claude Code (needs a ChatGPT account or an OpenAI API key and sends the draft to OpenAI) for the first read on a different model family; the bundled prose-clarity-reviewer is the fallback, on Claude's own model family. fact-checker and theme-reviewer use web search. The skill also routes to skills it does not bundle: session-theme-mining, collect-context, headline-craft, quality-gate and prose-translation (the first and fourth are in zenn-content's `.claude/skills/`, the other three in shimo4228's claude-harness repo); none is needed to reach the content GO.

Example: asking for a new article in a project with a channel contract (or running `/writing-ecosystem`) makes the skill ask what the writer wants to question, restate it as a thesis, and write an editorial brief to `docs/plans/<slug>.md` with the fields Reader, Channel, Author's words (verbatim), Central thesis, Entry bridge, Figure plan, Causal spine, Selected evidence and Out of scope; it then stops for the writer's confirmation. After drafting and structural freeze, the channel reviewer (editor or essay-reviewer), fact-checker and the first read each run once; the writer gives a content GO; headline-craft proposes titles, title-reviewer returns findings, the writer picks; quality-gate aggregates the evidence before the writer's publication GO.

Link map: [SKILL.md](skills/writing-ecosystem/SKILL.md) (current specification), [references/](skills/writing-ecosystem/references/) (style diagnostics, review output format, publication procedures), [agents/](agents/), [CHANGELOG.md](CHANGELOG.md), shimo4228's channel contract and writing principles in [zenn-content/.claude/rules/](https://github.com/shimo4228/zenn-content/tree/main/.claude/rules), and shimo4228's hub at https://github.com/shimo4228/shimo4228.

</details>
