# glab Troubleshooting Guide

Use this guide when `glab` behavior does not match the workspace defaults in `SKILL.md`.

## Quick triage

Start with read-only checks before changing auth, remotes, or shell configuration:

```zsh
glab --version
glab auth status
git remote -v
git status --short --branch
```

Those four commands usually reveal whether the problem is missing binary, wrong host, wrong repo context, or missing permissions.

## Missing binary

### Symptom

```text
command not found: glab
```

### What it usually means

- `glab` is not installed
- `glab` is installed outside the current `PATH`

### What to do

1. Confirm the binary is really missing:

   ```zsh
   glab --version
   which glab
   ```

2. Install `glab` with your platform package manager, then retry `glab --version`.

3. If the install succeeded but `which glab` still fails, update `PATH` for the current shell first:

   ```zsh
   export PATH="$PATH:/path/to/glab"
   which glab
   ```

4. Only persist that change in your shell startup file after you know the path is correct. In this workspace, prefer your zsh profile, not a bash-first fallback.

### References

- GitLab CLI docs: https://docs.gitlab.com/cli/
- GitLab CLI project: https://gitlab.com/gitlab-org/cli

## Unauthenticated or expired credentials

### Symptoms

```text
401 Unauthorized
```

or

```text
failed to get current user
```

### What it usually means

- No saved auth for the intended host
- Saved token expired or was revoked
- Command is hitting a different host than expected

### What to do

1. Inspect the current auth state before logging in again:

   ```zsh
   glab auth status
   ```

2. If the required host is missing, authenticate explicitly for that host:

   ```zsh
   glab auth login --hostname gitlab.vi.vector.int
   ```

3. If the host is present but the token is stale, re-run login for that same host.

4. Keep host selection narrow when testing another instance:

   ```zsh
   GITLAB_HOST=code.vector.cloud glab auth status
   ```

5. Retry the original command after auth succeeds.

### Why this order matters

Blindly re-running `glab auth login` can hide the real issue when the CLI is already authenticated, but against the wrong GitLab instance.

## Wrong host or unexpected GitLab instance

### Symptoms

- `glab` talks to `gitlab.com` when the workspace expects on-prem
- `glab auth status` only shows another host
- API calls return 401 or 404 even though the project exists

### What it usually means

- The command is using the wrong host context
- The needed host is not authenticated yet
- Repo targeting did not disambiguate host and namespace

### What to do

1. Check the known accounts first:

   ```zsh
   glab auth status
   ```

2. For this workspace, stay on `gitlab.vi.vector.int` unless the task explicitly targets `code.vector.cloud` or another host.

3. Prefer explicit repo targeting when host ambiguity is possible:

   ```zsh
   glab repo view -R gitlab.vi.vector.int/group/subgroup/project
   ```

4. If you only need a one-off host override, keep it scoped to that command:

   ```zsh
   GITLAB_HOST=code.vector.cloud glab mr list
   ```

5. If the host is missing from `glab auth status`, then log in with `--hostname` and retry.

## Wrong repository context

### Symptoms

```text
404 Project Not Found
```

or

```text
not a git repository
```

or the command opens the wrong project.

### What it usually means

- You are outside the intended checkout
- Current git remotes point at another project
- The project path is missing subgroup or host details

### What to do

1. Verify local repo context:

   ```zsh
   git remote -v
   git status --short --branch
   ```

2. If you are not inside the correct checkout, run the command from the right repo.

3. If you are working across repos, target the project explicitly:

   ```zsh
   glab mr list -R group/subgroup/project
   glab mr list -R gitlab.vi.vector.int/group/subgroup/project
   ```

4. Use host-qualified `-R/--repo` when same-name projects exist on more than one GitLab instance.

5. If you still see 404, confirm the namespace path in GitLab and verify that your account can see the project.

## Duplicate merge request for the same branch

### Symptom

```text
source branch already has a merge request
```

### What it usually means

An MR already exists for the current source branch.

### What to do

1. Find the existing MR:

   ```zsh
   glab mr list --source-branch "$(git branch --show-current)"
   ```

2. Open or inspect it:

   ```zsh
   glab mr view <mr-number>
   ```

3. Update the existing MR instead of creating another one:

   ```zsh
   glab mr update <mr-number> --title "Updated title"
   ```

4. If you expected a different repo or host, return to the wrong host and wrong repo checks above before trying again.

## Insufficient permissions

### Symptoms

```text
403 Forbidden
```

or

```text
insufficient permissions
```

or push / merge actions fail while read-only commands work.

### What it usually means

- The token lacks required scopes
- Your GitLab role is too limited for the action
- The project is private or protected in a way your account cannot modify

### What to do

1. Confirm which host and account are active:

   ```zsh
   glab auth status
   ```

2. Check whether the failing command is read-only or mutating. A token that can list projects may still fail on MR creation, pushes, or CI actions.

3. Re-authenticate with the correct host if the active account is wrong.

4. Verify the token scopes and the project role in GitLab.

5. If the repo is correct and auth is correct, treat this as a GitLab permission problem, not a shell problem.

## Helpful follow-up checks

Use these after the failure-specific sections above:

```zsh
glab <command> --help
glab api user
```

- `glab <command> --help` confirms the current CLI syntax before you rely on memory.
- `glab api user` is a fast auth smoke test for the current host context.

## What not to do by default

- Do not start by editing shell startup files before you confirm the real problem.
- Do not export `GITLAB_HOST` globally when a one-command override is enough.
- Do not create a new MR before checking whether the branch already has one.
- Do not assume a 404 means the project is missing. Wrong host and missing permissions can look the same.

## More help

- GitLab CLI docs: https://docs.gitlab.com/cli/
- GitLab CLI issues: https://gitlab.com/gitlab-org/cli/-/issues
