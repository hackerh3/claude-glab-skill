# Contributing to the glab skill

Keep contributions narrow, tested, and aligned with the hardened workflow guidance in `SKILL.md`.

## Source of truth

- Maintained fork: https://github.com/hackerh3/claude-glab-skill
- GitLab CLI docs root: https://docs.gitlab.com/cli/

When you update install, publish, or support text, point to those locations.

## What good changes look like

Prefer changes that do one of these:

- clarify preflight and host-selection behavior
- correct flags or examples against current `glab --help`
- add missing negative-path troubleshooting
- improve repo-targeting guidance for nested GitLab groups
- remove stale docs paths or GitHub fork references

## Writing rules

- match the concise workflow-first tone already used in `SKILL.md`
- prefer `zsh` fenced blocks for shell examples in this workspace
- don't default to bash-specific shell-profile remediation
- explain why a step matters when it avoids a common failure mode
- keep examples small and directly runnable

## Before you open a change

1. Verify the documented command shape:

   ```zsh
   glab --version
   glab <command> --help
   ```

2. If the text mentions auth or repo context, verify the guidance still matches the current skill rules in `SKILL.md`.

3. If you touch install or publishing text, confirm it still references the maintained fork.

4. Update `CHANGELOG.md` when user-facing docs change.

## Files to review together

Changes in one file often need a matching pass in another:

- `SKILL.md`, core policy and decision trees
- `README.md`, install and publish collateral
- `references/troubleshooting.md`, failure-path recovery
- `references/quick-reference.md`, concise command lookup
- `CHANGELOG.md`, user-visible documentation updates

## Common review checks

- current docs links use `https://docs.gitlab.com/cli/`
- maintained fork links use `https://github.com/hackerh3/claude-glab-skill`
- examples don't assume `gitlab.com` as the default workspace host
- repo examples use namespace-aware or host-qualified `-R/--repo` forms where ambiguity matters
- troubleshooting covers missing binary, unauthenticated access, wrong host, wrong repo, duplicate MR, and insufficient permissions

## Resources

- GitLab CLI project: https://gitlab.com/gitlab-org/cli
- GitLab CLI docs: https://docs.gitlab.com/cli/
- Claude Code skills docs: https://docs.claude.com/en/docs/claude-code/skills
