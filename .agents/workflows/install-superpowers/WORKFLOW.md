---
name: install-superpowers
metadata:
  version: 0.0.1
---

# Install Superpowers

## Upgrade

1. Find `https://github.com/obra/superpowers` and clone it into the `.context` directory.

```bash
mkdir -p .context
git clone git@github.com:obra/superpowers.git .context/superpowers
```

2. Delete the old local Superpowers skill directories. Do not use `*`; list every path explicitly.

```bash
rm -rf .agents/skills/brainstorming
rm -rf .agents/skills/dispatching-parallel-agents
rm -rf .agents/skills/executing-plans
rm -rf .agents/skills/finishing-a-development-branch
rm -rf .agents/skills/receiving-code-review
rm -rf .agents/skills/requesting-code-review
rm -rf .agents/skills/subagent-driven-development
rm -rf .agents/skills/systematic-debugging
rm -rf .agents/skills/test-driven-development
rm -rf .agents/skills/using-superpowers
rm -rf .agents/skills/verification-before-completion
rm -rf .agents/skills/writing-plans
```

3. Copy the new Superpowers skill directories into `.agents/skills`.

```bash
mkdir -p .agents/skills
cp -R .context/superpowers/skills/brainstorming .agents/skills/brainstorming
cp -R .context/superpowers/skills/dispatching-parallel-agents .agents/skills/dispatching-parallel-agents
cp -R .context/superpowers/skills/executing-plans .agents/skills/executing-plans
cp -R .context/superpowers/skills/finishing-a-development-branch .agents/skills/finishing-a-development-branch
cp -R .context/superpowers/skills/receiving-code-review .agents/skills/receiving-code-review
cp -R .context/superpowers/skills/requesting-code-review .agents/skills/requesting-code-review
cp -R .context/superpowers/skills/subagent-driven-development .agents/skills/subagent-driven-development
cp -R .context/superpowers/skills/systematic-debugging .agents/skills/systematic-debugging
cp -R .context/superpowers/skills/test-driven-development .agents/skills/test-driven-development
# Skipped: using-git-worktrees
cp -R .context/superpowers/skills/using-superpowers .agents/skills/using-superpowers
cp -R .context/superpowers/skills/verification-before-completion .agents/skills/verification-before-completion
cp -R .context/superpowers/skills/writing-plans .agents/skills/writing-plans
# Skipped: writing-skills
```

4. Wait for human review.

## Tuning

Only tune the following installed Superpowers skills:

- `brainstorming`
- `dispatching-parallel-agents`
- `executing-plans`
- `finishing-a-development-branch`
- `receiving-code-review`
- `requesting-code-review`
- `subagent-driven-development`
- `systematic-debugging`
- `test-driven-development`
- `using-superpowers`
- `verification-before-completion`
- `writing-plans`

All tuning must stay within these specified project-local skills under `.agents/skills`.

1. Remove Gemini-related content. We do not use Gemini.

Delete the Gemini tool mapping file:

```bash
rm .agents/skills/using-superpowers/references/gemini-tools.md
```

Then inspect the installed skills and remove or rewrite every Gemini-related reference. The intent is to avoid shipping any Gemini-specific tooling, instructions, examples, or platform guidance because this project does not use Gemini. Choose the appropriate search and edit approach based on the workspace and available tools.

2. Remove global skills and global configuration guidance. This project must use project-local skills under `.agents/skills`, not global skills or global tool configuration. Inspect the newly copied skill files and rewrite any instructions that tell users or agents to add, enable, install, load, or configure skills through global paths or global config files. For example, guidance such as adding settings to `~/.codex/config.toml` is incorrect for this project and should be removed or rewritten to project-local guidance.

3. Record the project Superpowers tuning in `using-superpowers`.

Do not globally rewrite the installed skills for this step. Only update `.agents/skills/using-superpowers/SKILL.md` so `using-superpowers` knows this project's tuned Superpowers setup.

The project intentionally installs and uses these Superpowers skills:

- `brainstorming`
- `dispatching-parallel-agents`
- `executing-plans`
- `finishing-a-development-branch`
- `receiving-code-review`
- `requesting-code-review`
- `subagent-driven-development`
- `systematic-debugging`
- `test-driven-development`
- `using-superpowers`
- `verification-before-completion`
- `writing-plans`

The project intentionally skips these Superpowers skills:

- `using-git-worktrees`
- `writing-skills`

When a skipped skill is referenced elsewhere, `using-superpowers` should guide the agent to treat that reference according to this project tuning instead of modifying every installed skill file.

4. Wait for human review.
