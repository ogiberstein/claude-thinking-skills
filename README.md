# claude-thinking-skills

Adversarial-review and structured-thinking skills for [Claude Code](https://claude.com/claude-code). Plain markdown, no execution surface — auditable by `cat`.

## Skills

| Skill | What it does | When it fires |
|-------|-------------|----------------|
| **[premortem](premortem/SKILL.md)** | Prospective failure analysis. Assume the change already failed; reverse-engineer the headline. | Before shipping a non-trivial change or committing to a decision |
| **[paranoid-review](paranoid-review/SKILL.md)** | Adversarial code review across six lenses: hostile input, concurrency, partial failure, resource exhaustion, trust boundaries, observability gaps | "What could break?", edge cases on a diff |
| **[mental-models](mental-models/SKILL.md)** | Munger-style lattice (inversion, second-order, opportunity cost, base rates, incentives, Goodhart, Hanlon, path dependence, etc.) | Strategic / business decisions — not code |
| **[steelman](steelman/SKILL.md)** | Anti-sycophancy circuit-breaker. Forces genuine evaluation when the user pushes back, rather than capitulating to social pressure | Multi-turn pushback, "are you sure", proposed alternatives — fires proactively |

These compose well: e.g. *"premortem this, then steelman the findings"* or *"apply mental models, then lateral-think any blind spots."*

## Install

```bash
# Clone alongside your other Claude Code skills
cd ~/.claude/skills
git clone https://github.com/ogiberstein/claude-thinking-skills.git

# Symlink each skill into ~/.claude/skills/ so Claude Code discovers them
cd ~/.claude/skills
for s in premortem paranoid-review mental-models steelman; do
  ln -s claude-thinking-skills/$s $s
done
```

Verify by starting a new Claude Code session and checking that the 4 skills appear in the available-skills list.

## Design principles

1. **Plain markdown only.** No scripts, no binaries, no `npx`. The trust surface is what you can read.
2. **Fire on specific triggers.** Each skill's frontmatter `description` field names concrete phrases that should invoke it — vague triggers cause dispatcher noise.
3. **Distinct from existing review tools.** Cross-references in each SKILL.md show how it differs from sibling skills (e.g. `paranoid-review` vs `/codex challenge` vs `/review`).
4. **Force a decision.** Every skill ends with an explicit verdict, not vague "be careful" theater.

## Related

- [ogiberstein/lateral-thinking-skill](https://github.com/ogiberstein/lateral-thinking-skill) — cross-domain divergent thinking (convergent counterpart to `mental-models`)
- [garrytan/gstack](https://github.com/garrytan/gstack) — broader Claude Code skill set; pairs well with these

## License

MIT
