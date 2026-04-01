# glab Commands - Detailed Reference

This is a comprehensive reference for all glab commands. This file is loaded when detailed command information is needed.

## Merge Requests (MR)

### Listing Merge Requests
```bash
# List MRs assigned to you
glab mr list --assignee=@me

# List MRs where you're a reviewer
glab mr list --reviewer=@me

# Check whether the current branch already has an MR
glab mr list --source-branch=$(git branch --show-current)

# List all open MRs
glab mr list

# Filter by state
glab mr list --state=merged
glab mr list --state=closed
glab mr list --state=all
```

### Creating Merge Requests

Before `glab mr create`, check whether the source branch already has an MR:

```bash
glab mr list --source-branch=$(git branch --show-current)
```

If one exists, inspect or update it instead of creating a duplicate:

```bash
glab mr view <mr-number>
glab mr update <mr-number> --draft
```

Keep new MRs draft-first in this workspace.

```bash
# Create MR from current branch (interactive)
glab mr create

# Create MR with title and description
glab mr create --title "Fix bug" --description "Fixes issue #123"

# Create draft MR
glab mr create --draft

# Create draft MR for a related issue
glab mr create --draft --related-issue 123

# Create MR and assign reviewers
glab mr create --reviewer=username1,username2

# Create MR with labels
glab mr create --label="bug,priority:high"

# Create MR with assignee
glab mr create --assignee=username

# Create MR to a specific target branch
glab mr create --target-branch=develop

# Create MR from commit info and skip prompts
# Note: --fill also sets push=true and pushes the branch.
glab mr create --fill --draft

# Create MR and remove source branch after merge
glab mr create --remove-source-branch
```

### Viewing and Interacting with MRs
```bash
# View MR details in the terminal
glab mr view 123

# Open MR in browser
glab mr view 123 --web

# View MR with comments
glab mr view 123 --comments

# Checkout MR branch locally
glab mr checkout 243

# Approve MR
glab mr approve 123

# Unapprove MR
glab mr unapprove 123

# Merge MR
glab mr merge 123

# Merge and delete source branch
glab mr merge 123 --remove-source-branch

# Close MR without merging
glab mr close 123

# Reopen closed MR
glab mr reopen 123

# Add note/comment to MR
glab mr note 123 -m "Looks good to me"

# Update MR title
glab mr update 123 --title "New title"

# Update MR description
glab mr update 123 --description "New description"

# Mark MR as draft
glab mr update 123 --draft

# Mark MR as ready (remove draft status)
glab mr update 123 --ready

# Refresh title and description from commit info
glab mr update 123 --fill --fill-commit-body --yes

# Subscribe to MR notifications
glab mr subscribe 123

# Unsubscribe from MR notifications
glab mr unsubscribe 123
```

## Issues

### Listing Issues
```bash
# List all issues
glab issue list

# List issues assigned to you
glab issue list --assignee=@me

# List issues with specific label
glab issue list --label=bug

# List issues with multiple labels
glab issue list --label="bug,priority:high"

# List closed issues
glab issue list --state=closed

# List all issues (open and closed)
glab issue list --state=all

# Search issues
glab issue list --search="login error"

# List issues assigned to specific user
glab issue list --assignee=username
```

### Creating and Managing Issues
```bash
# Create issue interactively
glab issue create

# Create issue with title and description
glab issue create --title "Bug in login" --description "Users cannot log in"

# Create issue with labels
glab issue create --title "Feature request" --label="enhancement,feature"

# Create issue with assignee
glab issue create --title "Fix bug" --assignee=username

# Create issue linked to an MR
glab issue create --title "Backport follow-up" --linked-mr 123

# Create confidential issue
glab issue create --title "Security issue" --confidential

# View issue details
glab issue view 456

# View issue in browser
glab issue view 456 --web

# Close issue
glab issue close 456

# Close with a comment
glab issue close 456 -m "Fixed in MR !123"

# Reopen issue
glab issue reopen 456

# Update issue title
glab issue update 456 --title "New title"

# Update issue description
glab issue update 456 --description "New description"

# Add labels to issue
glab issue update 456 --label="bug,confirmed"

# Assign issue
glab issue update 456 --assignee=username

# Subscribe to issue
glab issue subscribe 456

# Unsubscribe from issue
glab issue unsubscribe 456
```

## CI/CD Pipelines

### Viewing Pipelines
```bash
# Show available CI commands first
glab ci --help

# List recent pipelines
glab ci list

# List pipelines with specific status
glab ci list --status=failed
glab ci list --status=success
glab ci list --status=running

# View specific pipeline status
glab ci status

# View pipeline for a specific branch or ref
glab ci status --branch=develop

# Trace job logs from the latest matching pipeline
glab ci trace

# Trace a specific job by ID or job name
glab ci trace <job-id>
glab ci trace lint

# View the current pipeline for a branch or tag
glab ci view
glab ci view v1.2.3

# Delete a pipeline
glab ci delete <pipeline-id>
```

### Triggering and Managing Pipelines
```bash
# Create a new pipeline on the current branch
glab ci run

# Run pipeline for specific branch
glab ci run --branch=develop

# Run merge request pipeline instead of branch pipeline
glab ci run --mr

# Run pipeline with variables from the environment-style flag
glab ci run --variables-env KEY1:value1 --variables-env KEY2:value2

# Run pipeline with file and JSON-backed variables
glab ci run --variables-file CONFIG:.env.production
glab ci run --variables-from variables.json

# Run pipeline with typed inputs
glab ci run --input replicas:int(3) --input debug:bool(false)

# Trigger a manual job in an existing pipeline
glab ci trigger lint
glab ci trigger 224356863 --pipeline-id 991122

# Create a pipeline with a pipeline trigger token
glab ci run-trig --token xxxx --branch develop
glab ci run-trig --token xxxx --variables DEPLOY_ENV:staging

# Retry a job
glab ci retry

# Retry a specific job by ID or job name
glab ci retry <job-id>
glab ci retry lint

# Cancel a specific job
glab ci cancel job <job-id>

# Cancel a specific pipeline
glab ci cancel pipeline <pipeline-id>
```

### CI Configuration
```bash
# Lint .gitlab-ci.yml file in current directory
glab ci lint

# Lint a specific file path
glab ci lint .gitlab-ci.yml
glab ci lint path/to/.gitlab-ci.yml

# Simulate pipeline creation for a ref
glab ci lint --dry-run --ref develop --include-jobs

# View CI configuration
glab ci config

# Get CI job artifacts
glab ci artifact <job-id>

# Download artifacts to specific path
glab ci artifact <job-id> -p path/to/download
```

## Repository Operations

### Cloning Repositories
```bash
# Clone a repository by namespace path
glab repo clone group/subgroup/project

# Clone to specific directory
glab repo clone group/subgroup/project target-dir

# Clone by full URL or project ID
glab repo clone https://gitlab.vi.vector.int/group/subgroup/project
glab repo clone 4356677

# Clone from self-hosted GitLab
GITLAB_HOST=gitlab.vi.vector.int glab repo clone group/subgroup/project

# Clone repositories from a group
glab repo clone -g groupname
glab repo clone -g groupname --archived=false --paginate

# Pass native git clone flags after --
glab repo clone group/subgroup/project -- --branch develop --depth 1
```

Notes:
- `glab repo clone` uses the configured protocol. Do not invent a `--protocol` flag when the current help output does not show one.
- Only mention automatic `upstream` remote creation when cloning a fork the current authenticated user owns, because that side effect is conditional.

### Repository Information and Management
```bash
# View repository details
glab repo view

# View a specific repository
glab repo view group/subgroup/project

# View in browser
glab repo view --web

# Fork repository
glab repo fork

# Fork to specific namespace
glab repo fork --clone --namespace=mygroup

# Archive repository
glab repo archive owner/project

# Unarchive repository
glab repo unarchive owner/project

# Delete repository
glab repo delete project [group/subgroup/]

# Create repository
glab repo create project-name

# Create repository under a group or namespace path
glab repo create group/subgroup/project-name

# Create private repository
glab repo create project-name --private

# Create repository with description
glab repo create project-name --description "My project"

# Set an explicit default branch when that matters
glab repo create project-name --defaultBranch develop

# Create on a specific host
GITLAB_HOST=gitlab.vi.vector.int glab repo create project-name
glab repo create gitlab.vi.vector.int/group/subgroup/project-name

# Mirror repository
glab repo mirror source-repo target-repo
```

## API Access

### Making API Calls

Keep these API rules straight before reaching for examples:

- Default method is `GET` when no parameters are added, and `POST` otherwise. `--field` and `--raw-field` count as parameters, so force `--method` when the default would be misleading.
- Keep pagination controls inside the endpoint string, for example `?page=2&per_page=100`. Do not document a `glab api --per-page` pattern.
- Use `--raw-field key=value` for string values as typed.
- Use `--field key=value` when you want type inference. It can coerce booleans, `null`, and integers, fill repository placeholders such as `:branch`, and read values from `@file` or `@-`.
- Use `--input <file>` for a raw request body. In that mode, `--field` values are serialized into URL query parameters instead of the JSON body.
- For GraphQL, fields other than `query` and `operationName` become variables.
- Mention GraphQL `--paginate` only when the query accepts `$endCursor` and fetches `pageInfo { hasNextPage endCursor }` from the paginated collection.

```bash
# GET request
glab api projects/:id/merge_requests

# GET with specific project ID
glab api projects/12345/merge_requests

# GET with explicit query-string pagination
glab api "projects/:id/issues?page=2&per_page=100"

# Auto-paginate REST results, keep per_page in the endpoint string
glab api --paginate "projects/:id/merge_requests?per_page=50&state=opened"

# POST request with inferred JSON types
glab api --method POST projects/:id/issues --field title="Bug report"

# POST with multiple inferred fields
glab api --method POST projects/:id/issues \
  --field title="Bug" \
  --field description="Description here" \
  --field labels="bug,priority:high"

# POST with exact string fields
glab api --method POST projects/:id/issues \
  --raw-field title="Bug" \
  --raw-field labels="bug,priority:high"

# PUT request
glab api --method PUT projects/:id/merge_requests/1 --field title="New Title"

# DELETE request
glab api --method DELETE projects/:id/issues/123

# GET with query-string pagination only
glab api "projects/:id/issues?per_page=100"

# Send a raw body from file
glab api --method POST --input issue.json projects/:id/issues

# Raw body plus URL query parameters from --field
glab api --method POST --input body.json --field sudo=root projects/:id/issues

# Include response headers
glab api --include projects/:id

# Silent mode (no progress)
glab api --silent projects/:id/merge_requests

# GraphQL request, query stays a string
glab api graphql --raw-field query='query { currentUser { username } }'

# GraphQL pagination needs $endCursor and pageInfo in the query
glab api graphql --paginate --raw-field query='query($endCursor: String) { project(fullPath: "gitlab-org/graphql-sandbox") { issues(first: 2, after: $endCursor) { nodes { title } pageInfo { endCursor hasNextPage } } } }'
```

## Labels

```bash
# List all labels
glab label list

# Create label
glab label create "bug" --color="#FF0000"

# Create label with description
glab label create "feature" --color="#00FF00" --description "New features"

# Delete label
glab label delete "old-label"
```

## Releases

```bash
# List releases
glab release list

# Create release
glab release create v1.0.0

# Create release with notes
glab release create v1.0.0 --notes "Release notes here"

# Create release from file
glab release create v1.0.0 --notes-file CHANGELOG.md

# Create release with assets
glab release create v1.0.0 --asset-links='[{"name":"Asset","url":"https://..."}]'

# View specific release
glab release view v1.0.0

# Download release assets
glab release download v1.0.0

# Delete release
glab release delete v1.0.0
```

## Snippets

```bash
# List snippets
glab snippet list

# List all snippets (including private)
glab snippet list --all

# Create snippet
glab snippet create --title "Config" --filename config.yml

# Create snippet from file
glab snippet create --title "Script" myfile.sh

# Create private snippet
glab snippet create --title "Secret" --private secret.txt

# View snippet
glab snippet view <snippet-id>

# Delete snippet
glab snippet delete <snippet-id>
```

## User Operations

```bash
# View current user information
glab user view

# View specific user
glab user view username

# List user's events
glab user events

# List specific user's events
glab user events username
```

## Variables (CI/CD)

```bash
# List variables
glab variable list

# Get specific variable
glab variable get VAR_NAME

# Set/create variable
glab variable set VAR_NAME value

# Set protected variable
glab variable set VAR_NAME value --protected

# Set masked variable
glab variable set VAR_NAME value --masked

# Update variable
glab variable update VAR_NAME new-value

# Delete variable
glab variable delete VAR_NAME

# Export variables
glab variable export > variables.json

# Import variables
glab variable import < variables.json
```

## Additional Commands

### Aliases
```bash
# Create alias
glab alias set co "mr checkout"

# List aliases
glab alias list

# Delete alias
glab alias delete co
```

### SSH Keys
```bash
# List SSH keys
glab ssh-key list

# Add SSH key
glab ssh-key add ~/.ssh/id_rsa.pub

# Add SSH key with title
glab ssh-key add ~/.ssh/id_rsa.pub --title "Work laptop"

# Delete SSH key
glab ssh-key delete <key-id>
```

### Deploy Keys
```bash
# List deploy keys
glab deploy-key list

# Add deploy key
glab deploy-key add --title "CI/CD" --key "ssh-rsa ..."

# Delete deploy key
glab deploy-key delete <key-id>
```

### Schedules (Pipeline Schedules)
```bash
# List pipeline schedules
glab schedule list

# Create schedule
glab schedule create --cron "0 2 * * *" --ref main --description "Nightly build"

# Run schedule immediately
glab schedule run <schedule-id>

# Delete schedule
glab schedule delete <schedule-id>
```

## Common Flags Across Commands

Most glab commands support these common flags:

- `--help`, `-h` - Show help for command
- `--repo`, `-R` - Specify repository, usually `group/subgroup/project`, and some commands also accept full URL or Git URL
- `--web`, `-w` - Open in web browser
- `--output`, `-o` - Output format (json, text, etc.)
- `--verbose` - Enable verbose output
- `--page`, `-p` - Page number for paginated results
- `--per-page`, `-P` - Number of items per page

## Output Formats

Many commands support different output formats:

```bash
# JSON output (useful for scripting)
glab mr list --output=json

# Pipe to jq for processing
glab mr list --output=json | jq '.[] | .title'
```

## Configuration Commands

```bash
# View all configuration
glab config get

# Get specific config value
glab config get editor

# Set configuration value
glab config set editor vim

# Common config keys:
# - editor: preferred text editor
# - browser: web browser to use
# - glamour_style: style for terminal rendering
# - host: default GitLab host
```

## Completion

```bash
# Generate completion script for bash
glab completion --shell bash

# For zsh
glab completion --shell zsh

# For fish
glab completion --shell fish

# For PowerShell
glab completion --shell powershell

# Install completion (bash example)
glab completion --shell bash > /etc/bash_completion.d/glab
```

## Version and Updates

```bash
# Show glab version
glab version

# Check for updates
glab check-update

# View changelog
glab changelog
```
