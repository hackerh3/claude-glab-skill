# GitLab CLI `glab` skill

Focused Claude Code skill for GitLab CLI work in environments that need explicit host, auth, and repo targeting.

## Maintained fork

Use the maintained fork for installs, updates, and publishing context:

- Repository: https://github.com/hackerh3/claude-glab-skill

## What this skill covers

- preflight checks before mutating commands
- host-aware auth guidance for `gitlab.vi.vector.int` by default
- repo targeting rules for nested GitLab groups
- draft-first merge request workflows
- progressive disclosure references for deeper command help

## Install the skill

Clone the maintained fork into your Claude Code skills directory:

```zsh
# project-local install
mkdir -p .claude/skills
git clone https://github.com/hackerh3/claude-glab-skill .claude/skills/glab

# personal install
mkdir -p ~/.claude/skills
git clone https://github.com/hackerh3/claude-glab-skill ~/.claude/skills/glab
```

## Install `glab`

Install the GitLab CLI before using the skill.

### macOS

```zsh
brew install glab
```

### Linux

```zsh
sudo apt install glab
```

### Windows

```powershell
choco install glab
```

More install options and upstream CLI docs live at https://docs.gitlab.com/cli/.

## Use the skill

Examples:

```text
Use the glab skill to check my merge request pipeline
Use the glab skill to create a draft MR on gitlab.vi.vector.int
Use the glab skill to inspect issue 123 in group/subgroup/project
```

## Documentation layout

- `SKILL.md`, core guidance loaded on invocation
- `references/quick-reference.md`, fast command lookup
- `references/commands-detailed.md`, deeper flags and command families
- `references/troubleshooting.md`, negative-path diagnosis and recovery

## Troubleshooting focus

The troubleshooting reference now centers on the failure paths that matter most in this workspace:

- missing `glab` binary
- unauthenticated or expired credentials
- wrong GitLab host
- wrong repo context
- duplicate merge requests for the same branch
- insufficient permissions

It also avoids bash-centric shell advice. If persistence is needed locally, prefer a zsh startup file in this workspace.

## Publish and maintenance notes

- Keep references aligned with the maintained fork: https://github.com/hackerh3/claude-glab-skill
- Point GitLab CLI docs at https://docs.gitlab.com/cli/
- Treat `gitlab.vi.vector.int` as the default workspace host unless a task explicitly targets another instance

## Contributing

See `CONTRIBUTING.md` for documentation rules, testing expectations, and release-note updates.
