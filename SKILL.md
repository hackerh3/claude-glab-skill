---
name: glab
description: Expert guidance for using the GitLab CLI (glab) to manage GitLab issues, merge requests, CI/CD pipelines, repositories, and related GitLab operations from the command line.
allowed-tools: [Bash, Read, Grep, Glob]
---

# GitLab CLI (`glab`) Skill

Use this skill when the user needs to work with GitLab from the terminal with `glab`.

## When to Use This Skill

Invoke when the user needs to:
- Inspect or manage merge requests
- Inspect or manage issues
- Check CI/CD status or traces
- Target a GitLab repo from the terminal
- Use `glab api` for GitLab API access

## Preflight

Start with read-only checks before mutating anything:

```bash
glab --version
glab auth status
git remote -v
git status --short --branch
```

If `glab` is missing, stop and point the user to the upstream install docs or package manager instructions.

## Auth and Host Selection

Default workspace target:
- Host: `gitlab.vi.vector.int`
- Target: on-prem unless the user explicitly asks for another host

Decision tree:

1. Check whether `glab` is installed.
   - Run `glab --version`.
   - If that fails, stop there and point the user to install `glab` first.
   - If it succeeds, continue with auth checks.
2. Check the current auth state before suggesting any login flow.
   - Run `glab auth status`.
   - If the needed host already appears and is authenticated, keep using that host.
   - If the output only shows another host, switch the command context explicitly instead of assuming the default changed.
   - If the needed host is missing or unauthenticated, then `glab auth login --hostname <host>` becomes the next setup step.
3. Choose the host explicitly when the user asks for something other than the workspace default.
   - Stay on `gitlab.vi.vector.int` unless the user explicitly asks for another GitLab instance such as `code.vector.cloud`.
   - For alternate-host work, prefer per-command host selection with `--hostname` when the command supports it, or a narrow `GITLAB_HOST=<host> ...` prefix when that is the documented shape.
4. Treat environment variables as a scoped override, not the default fix.
   - Use `GITLAB_HOST` or `GITLAB_TOKEN` only when you need to target a different host or a non-default credential path.
   - Do not present exported env vars as the preferred universal solution when `glab auth status` already shows the right host.

Examples:

```bash
glab --version
glab auth status
glab auth login --hostname gitlab.vi.vector.int
GITLAB_HOST=code.vector.cloud glab auth status
```

Scoped env-var examples:

```bash
GITLAB_HOST=gitlab.vi.vector.int glab repo view
GITLAB_TOKEN=your-token glab auth status
```

Prefer read-only host discovery first, then only suggest login or env-var overrides when the observed auth state requires them.

## Repo Targeting

Decision tree:

1. Check whether the current directory already gives the right repo context.
   - Run `git remote -v` and `git status --short --branch`.
   - If you are inside the intended checkout and its remotes point at the right GitLab project, current repo context is usually the simplest choice.
   - If you are outside a Git repo, inside the wrong repo, or working across several repos, switch to explicit targeting with `-R/--repo`.
2. Use current repo context only when it is clearly correct.
   - Good fit: `glab repo view`, `glab mr list`, or `glab ci status` from the intended checkout.
   - Not a good fit: running from a parent directory, a different clone, or automation that targets a repo other than the current working tree.
3. Use `-R/--repo` when repo context is ambiguous or intentionally remote.
   - Prefer `-R/--repo namespace/project` for a same-host target when the namespace path is clear.
   - Prefer a host-qualified target such as `gitlab.vi.vector.int/group/subgroup/project` when you need to remove host ambiguity.
   - Use explicit targeting for cross-project queries even if you are inside another checkout.
4. Do not rely on GitHub-style shorthand as the only model.
   - GitLab projects often live under nested groups like `group/subgroup/project`.
   - `owner/repo` can be accepted in some contexts, but it is not the safest default in this workspace.

Current repo context examples:

```bash
git remote -v
git status --short --branch
glab repo view
glab mr list
```

Explicit `-R/--repo` examples:

```bash
glab repo view -R group/subgroup/project
glab mr list -R gitlab.vi.vector.int/group/subgroup/project
glab issue list --repo namespace/project
glab ci status -R gitlab.vi.vector.int/group/subgroup/project
```

Prefer current repo context for the active checkout, but switch to explicit namespace-aware or host-qualified `-R/--repo` targets as soon as the working directory stops being a trustworthy source of truth.

## Workflow Families

### Merge Requests

- Check for an existing MR before creating a new one
- Keep MRs as draft by default until the user is ready for review
- Prefer read-only inspection commands first

Decision tree:

1. Start with read-only MR discovery.
   - Run `glab mr list --assignee=@me`, `glab mr list --reviewer=@me`, or `glab mr view <mr-number>` when you first need context.
2. Before `glab mr create`, check whether the branch already has an MR.
   - Run `glab mr list --source-branch=<branch-name>` for the current branch.
   - If a matching MR already exists, inspect it with `glab mr view <mr-number>` and update it instead of creating a duplicate.
3. Keep new MRs draft-first in this workspace.
   - Prefer `glab mr create --draft ...` when opening a new MR.
   - Only move it forward with `glab mr update <mr-number> --ready` when the user is ready for review.
4. Treat `--fill` as a mutating shortcut, not a harmless metadata helper.
   - `glab mr create --fill` skips prompts, uses commit info, and also pushes the branch.
   - Use it only when that push side effect is acceptable.
5. Use the documented issue-linking flags instead of shorthand guesses.
   - For MR creation from an issue, prefer `glab mr create --related-issue <issue-iid>`.

Common starting points:

```bash
glab mr list --assignee=@me
glab mr list --reviewer=@me
glab mr list --source-branch=<branch-name>
glab mr view <mr-number>
glab mr checks <mr-number>
```

When creating or updating an MR, verify branch and remote state first. If the source branch already has a merge request, inspect or update it instead of creating another one. Keep draft creation ahead of merge guidance so review only starts when the branch is actually ready.

### Issues

- Inspect issue state before editing
- Link MR and issue work clearly when the user asks for it

Issue creation notes:
- Check `glab issue create --help` for current linking flags before documenting uncommon issue flows.
- When the user wants issue-to-MR linkage at creation time, prefer the documented flags such as `--linked-mr` on `glab issue create` or `--related-issue` on `glab mr create`.

Common starting points:

```bash
glab issue list --assignee=@me
glab issue view <issue-number>
glab issue create --help
```

### CI/CD

Keep the command families distinct:
- `glab ci status` for current status
- `glab ci trace` for job logs
- `glab ci lint` for CI config validation
- `glab ci run` to create a new pipeline
- `glab ci trigger` to start a manual job in an existing pipeline
- `glab ci run-trig` to create a pipeline with a pipeline trigger token

Quick decision rule:
- Start with `status`, `trace`, or `lint` for read-only investigation.
- Use `run` when the user wants a fresh pipeline, `trigger` when they mean a manual job button, and `run-trig` only when they already have a trigger token or CI job token flow.

Common starting points:

```bash
glab ci status
glab ci trace
glab ci lint
glab ci --help
```

### Repositories

- Confirm repo context before clone, fork, or create actions
- Use `-R/--repo` when current checkout is not the intended target
- Keep examples namespace-aware, and avoid promising clone or remote side effects unless the current `glab repo clone --help` output documents them.
- Do not assume a default branch name in repo examples. `glab repo create` exposes `--defaultBranch`, and defaults can vary by host or CLI behavior.

Common starting points:

```bash
glab repo view
glab repo clone group/subgroup/project
GITLAB_HOST=gitlab.vi.vector.int glab repo clone group/subgroup/project
glab repo --help
```

### API

Use `glab api` when higher-level commands do not cover the task.

Keep these rules straight:
- Pagination belongs in the endpoint query string, not a `--per-page` flag
- `--field` and `--raw-field` add parameters, so they change the default method to `POST` unless `--method` is set
- `--input` sends the raw request body, and any `--field` flags become URL query parameters in that mode
- Mention GraphQL pagination only when the query accepts `$endCursor` and fetches `pageInfo { hasNextPage endCursor }`
- Prefer small REST examples that match current help output

Examples:

```bash
glab api projects/:id/merge_requests
glab api "projects/:id/jobs?per_page=100"
glab api --paginate "projects/:id/pipelines/123/jobs?per_page=100"
glab api graphql --raw-field query='query { currentUser { username } }'
glab api --method POST projects/:id/issues --field title="Bug" --field description="Details"
```

## Best Practices

1. Run preflight checks before mutating commands
2. Confirm host and auth state before assuming login is broken
3. Prefer current-repo context when it is correct, otherwise use `-R/--repo`
4. Keep MR work draft-first in this workspace
5. Use `--help` for exact flags before documenting or running uncommon subcommands

## Progressive Disclosure

Load detailed references only when needed:
- `references/commands-detailed.md` for exact flags and broader command families
- `references/quick-reference.md` for fast command lookup
- `references/troubleshooting.md` for setup, auth, repo-context, and permission problems

Reach for the references when:
- The user needs exact command variants
- The task moves beyond the core workflow skeleton here
- You hit auth, host, repo, CI, or API edge cases

## Quick Fixes

**`command not found: glab`**
- Install `glab`, then rerun `glab --version`

**`401 Unauthorized` or auth failures**
- Run `glab auth status`
- Confirm the active host is the intended one
- Use `glab auth login --hostname gitlab.vi.vector.int` only if auth is actually missing

**`404 Project Not Found`**
- Check repo path and namespace
- Retry with `-R group/subgroup/project` or a host-qualified target

**`not a git repository`**
- Run from the correct checkout, or target the repo explicitly with `-R/--repo`

**`source branch already has a merge request`**
- Use `glab mr list` or `glab mr view` for the existing branch MR, then update that MR instead of creating a new one

## Notes

- `glab` auto-detects repo context from Git remotes when possible
- Host selection matters in this workspace because on-prem is the default
- Read-only checks are the safest first step for most support and documentation tasks
