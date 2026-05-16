# Changelog

All notable changes to this project will be documented in this file.

## [0.1.0] — 2026-05-16

Initial public release.

### What it does

A Claude Code skill that orchestrates the human-facing writing and review
ecosystem — articles, essays, blog posts, newsletter issues, and other
long-form human-primary content.

Holds the canonical AI-slop banned list (Japanese + English), Voice rules
(だ/である × 発見調), title conventions, and the role-boundary map across
`article-writing`, `editor`, `essay-reviewer`, and `fact-checker`.

### Components

- `SKILL.md` — ecosystem map (Write / Review × Quality / Logic / Facts matrix),
  AI-slop banned lists (JA + EN), Voice & Tone rules, three-stage question
  architecture, title conventions, section-length guidelines, project-overlay
  extension slot.

### Key contributions

- **AI-slop banned list** — concrete substitutions, not vague advice
- **Voice convention** — declarative tense (だ/である), discovery tone (〜だった
  / 〜と気づいた); strategic weakening of conclusions into rhetorical questions
  modeled on early Buddhist 阿含経 patterns
- **Title conventions** — concrete, honest, question-form OK, no emotional
  clickbait
- **Three-stage question architecture** — title + thesis-shape + mid-article
  rhetorical questions so the conclusion is reached with the reader
- **Audience boundary** — strictly human-primary; AI-facing documents go to
  `claude-skill-llms-txt-writer`

### Requirements

- No runtime dependencies. Documentation-only skill.

### Companion skills

- `claude-skill-llms-txt-writer` — AI-facing documents
- `article-writing` — general drafting framework (Banned Patterns subsumed)
- `editor` / `essay-reviewer` / `fact-checker` agents (in `claude-harness`)
