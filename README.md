# dotfiles

## Agent skills

`agents/skills/` holds skills in the [Agent Skills](https://agentskills.io) format.
Each harness reads the same `SKILL.md` files.

| Skill | Purpose |
| --- | --- |
| `pick-up-issue` | Take a Jira or GitHub issue from reading to an open PR. |
| `anti-surrender` | Check human understanding before code changes and before handoff. |

`agents/openai.yaml` files hold Codex UI metadata. Other harnesses ignore them.

Link each skill into the skill folder of each harness.
First remove any real folder with the same name (for example `~/.codex/skills/pick-up-issue`).
If you do not, `ln` puts the link inside that folder.

```bash
for h in ~/.agents ~/.claude ~/.codex; do
  mkdir -p "$h/skills"
  for s in "$PWD"/agents/skills/*/; do ln -sfn "${s%/}" "$h/skills/$(basename "$s")"; done
done
```
