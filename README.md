# dotfiles

## Agent skills

`agents/skills/` holds skills in the [Agent Skills](https://agentskills.io) format.
Each harness reads the same `SKILL.md` files.

| Skill | Purpose |
| --- | --- |
| `pick-up-issue` | Take a Jira or GitHub issue from reading to an open PR. |
| `create-pr` | Open a PR whose description explains intent, follows the repo template, and links the issue. |
| `anti-surrender` | Check human understanding before code changes and before handoff. |

`agents/openai.yaml` files hold Codex UI metadata. Other harnesses ignore them.

Install with the [`skills` CLI](https://github.com/vercel-labs/skills).
It puts one copy in `~/.agents/skills/` (Codex reads it there) and symlinks it for Claude Code:

```bash
npx skills add rossilor95/dotfiles -g -s '*' -a claude-code -a codex -y
```

After you push a change, get it with `npx skills update -g`.
First remove any real folder with the same skill name in `~/.claude/skills` or `~/.codex/skills`.
An old copy there can hide the installed skill.
