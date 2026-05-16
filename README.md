# claude-skill-writing-ecosystem

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) that orchestrates the **human-facing writing & review ecosystem** — articles, essays, blog posts, newsletter issues, and other long-form human-primary content.

Holds the canonical AI-slop banned list (Japanese + English), Voice rules (だ/である × 発見調 / declarative × discovery tone), title conventions, and the role-boundary map across `article-writing`, `editor`, `essay-reviewer`, and `fact-checker`.

> For AI-facing documents (`llms.txt` / `llms-full.txt` / FAQ pages / glossaries), use [claude-skill-llms-txt-writer](https://github.com/shimo4228/claude-skill-llms-txt-writer) instead. Audience separation is enforced by these two skills owning distinct concerns.

## Install

### Claude Code

```bash
# Copy skill into your global skills directory
cp -r skills/writing-ecosystem ~/.claude/skills/writing-ecosystem
```

No runtime dependencies. The skill is documentation-only.

### SkillsMP

```bash
/skills add shimo4228/claude-skill-writing-ecosystem
```

## Ecosystem map

The skill enforces a **2D role separation** — `Write / Review` × `Quality / Logic / Facts`:

| Phase | Component | Axis | Trigger |
|---|---|---|---|
| **Write** | `article-writing` skill | general drafting | any writing task — first drafts, structure |
| **Review: Quality** | `editor` agent | tech-article structure, code, AI slop, terminology | tech-article review |
| **Review: Logic** | `essay-reviewer` agent | idea-article logic, overload, tone | idea-article review |
| **Review: Facts** | `fact-checker` agent | web-verification of factual claims | before publication |
| **Shared** | `writing-ecosystem` skill (this one) | AI slop / Voice / ecosystem map | auto-loaded on writing + review |
| **Overlay** | `<project>/.claude/rules/<platform>-writing.md` | platform-specific rules | inside the project only |

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
| AI search engines / LLM agents (`llms.txt`, `llms-full.txt`, FAQ, glossary) | [claude-skill-llms-txt-writer](https://github.com/shimo4228/claude-skill-llms-txt-writer) |

The two skills do not overlap. Mixing audiences in one document makes both worse.

## Related

- [`article-writing`](https://github.com/shimo4228/agent-knowledge-cycle) — general writing framework (its Banned Patterns are subsumed by this skill's superset)
- [`editor`](https://github.com/shimo4228/claude-harness) agent — tech-article review
- [`essay-reviewer`](https://github.com/shimo4228/claude-harness) agent — idea-article review
- [`fact-checker`](https://github.com/shimo4228/claude-harness) agent — web-based fact verification
- [claude-skill-llms-txt-writer](https://github.com/shimo4228/claude-skill-llms-txt-writer) — AI-facing documents (`llms.txt` etc.)

## License

MIT. See [LICENSE](LICENSE).
