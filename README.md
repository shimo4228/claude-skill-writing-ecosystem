# claude-skill-writing-ecosystem

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/shimo4228/claude-skill-writing-ecosystem)

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) that orchestrates the **human-facing writing & review ecosystem** — articles, essays, blog posts, newsletter issues, and other long-form human-primary content.

Holds the canonical AI-slop banned list (Japanese + English), Voice rules (だ/である × 発見調 / declarative × discovery tone), title conventions, and the role-boundary map across `editor`, `essay-reviewer`, `prose-clarity-reviewer`, `theme-reviewer`, `title-reviewer`, and `fact-checker`.

> For AI-facing documents (`llms.txt` / `llms-full.txt` / FAQ pages / glossaries), use [llms-txt-writer](https://github.com/shimo4228/llms-txt-writer) instead. Audience separation is enforced by these two skills owning distinct concerns.

## Install

This repo bundles **both the skill and the agents it orchestrates** (`editor` / `essay-reviewer` / `prose-clarity-reviewer` / `theme-reviewer` / `title-reviewer` / `fact-checker`). The agents read their canonical rules (AI-slop list, Voice, title conventions) from the `writing-ecosystem` skill, so the skill and its agents must be installed together.

### Option A — one command (recommended)

```bash
git clone https://github.com/shimo4228/claude-skill-writing-ecosystem
cd claude-skill-writing-ecosystem
./install.sh
```

Copies `skills/*` into `~/.claude/skills/` and `agents/*.md` into `~/.claude/agents/`. Existing files are backed up to `*.bak-<timestamp>` first (use `--force` to skip backups, `--dry-run` to preview).

### Option B — manual

```bash
cp -r skills/writing-ecosystem ~/.claude/skills/writing-ecosystem
cp agents/*.md ~/.claude/agents/
```

No runtime dependencies; the skill is documentation-only.

> This skill grew out of the `article-writing` skill from [Everything Claude Code](https://github.com/affaan-m/everything-claude-code) (MIT) and now carries its own drafting flow, so ECC is no longer required. See Acknowledgements.

### SkillsMP

```bash
/skills add shimo4228/claude-skill-writing-ecosystem
```

> **Caveat:** SkillsMP installs `skills/` only — not `agents/`. After `/skills add`, copy the agents: `cp agents/*.md ~/.claude/agents/` (or use Option A).

## Ecosystem map

Who owns what, and when each runs. Which channel routes to `editor` or `essay-reviewer` is decided by
the project's channel table, not by article type.

| Phase | Component | Owns | Trigger |
|---|---|---|---|
| **Theme review** | `theme-reviewer` agent | findings on a chosen question; no verdict | before the editorial brief |
| **Write** | `writing-ecosystem` skill (this one) | central thesis, causal spine, evidence selection, structure | first draft, full revision |
| **Review: channel** | `editor` / `essay-reviewer` agent | structure, code accuracy, AI slop, terminology / logic, overload, tone | after structural freeze |
| **Review: clarity** | `prose-clarity-reviewer` agent | first screen, paragraph density, terminology first use, coined-term budget | after structural freeze |
| **Review: facts** | `fact-checker` agent | web verification of factual claims | before publication |
| **Title review** | `title-reviewer` agent | title-body contract; findings only | after the author's content GO |
| **Overlay** | `<project>/.claude/rules/*.md` | channel, reader, register, reviewer panel | inside the project only |

## Key contributions

- **AI-slop banned list (JA + EN)** — concrete substitutions, not vague advice. e.g. 「画期的」/ "powerful tool" / "revolutionize" — banned, with replacement strategies
- **Voice convention** — だ/である × 発見調. Declarative tense, discovery tone. Strategy for *weakening conclusions* into rhetorical questions (modeled on early Buddhist 阿含経 patterns)
- **Title conventions** — concrete, honest, question-form OK, no emotional clickbait
- **Three-stage question architecture** — title-level + thesis-shape + mid-article rhetorical questions, so the conclusion is reached *with* the reader rather than imposed
- **Project overlay slot** — platform-specific rules (Zenn character limits, Qiita tag conventions, corporate blog restrictions) extend the base via `<project>/.claude/rules/`

## Audience boundary

| Audience | Skill |
|---|---|
| Humans (articles, essays, blog posts, newsletters) | `writing-ecosystem` (this skill) |
| AI search engines / LLM agents (`llms.txt`, `llms-full.txt`, FAQ, glossary) | [llms-txt-writer](https://github.com/shimo4228/llms-txt-writer) |

The two skills do not overlap. Mixing audiences in one document makes both worse.

## Related

- [`editor`](agents/editor.md) agent — practical-channel review (**bundled** in this repo's `agents/`)
- [`essay-reviewer`](agents/essay-reviewer.md) agent — essay-channel review (**bundled**)
- [`prose-clarity-reviewer`](agents/prose-clarity-reviewer.md) agent — first-contact clarity; owns the density and terminology thresholds (**bundled**)
- [`theme-reviewer`](agents/theme-reviewer.md) agent — pre-writing question review (**bundled**)
- [`title-reviewer`](agents/title-reviewer.md) agent — title-body contract check (**bundled**)
- [`fact-checker`](agents/fact-checker.md) agent — web-based fact verification (**bundled**)
- [`article-writing`](https://github.com/affaan-m/everything-claude-code/tree/main/skills/article-writing) — the **Everything Claude Code (ECC)** skill by Affaan Mustafa (MIT) this one started from. Not bundled and no longer required; its Banned Patterns are subsumed here
- [llms-txt-writer](https://github.com/shimo4228/llms-txt-writer) — AI-facing documents (`llms.txt` etc.)

The bundled agents are also maintained in [claude-harness](https://github.com/shimo4228/claude-harness) (upstream origin); this repo vendors them so the ecosystem installs as one unit.

## Acknowledgments

The `article-writing` skill this one grew out of is **not authored by this repo**. It comes from [Everything Claude Code (ECC)](https://github.com/affaan-m/everything-claude-code) by [Affaan Mustafa](https://github.com/affaan-m), MIT-licensed. This skill (`writing-ecosystem`) layers a superset of conventions on top of it — AI-slop banned list, Voice rules, title rules, role boundaries — but the general drafting framework itself is ECC's contribution.

Thank you to ECC and to Affaan Mustafa for the foundational `article-writing` skill this ecosystem grew out of.

## License

MIT. See [LICENSE](LICENSE).
