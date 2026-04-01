# glab Quick Reference Guide

A condensed reference for the most commonly used GitLab CLI commands.

## Authentication

```zsh
glab auth status                                 # Check auth status first
glab auth login --hostname gitlab.vi.vector.int  # Login to workspace default host
GITLAB_HOST=code.vector.cloud glab auth status   # Scoped alternate-host check
```

## Merge Requests

```zsh
# Listing
glab mr list                       # All open MRs
glab mr list --assignee=@me        # MRs assigned to me
glab mr list --reviewer=@me        # MRs for me to review
glab mr list --source-branch=feat  # Check whether a branch already has an MR

# Creating
glab mr create                     # Interactive creation
glab mr create --title "Fix" --description "Desc"
glab mr create --draft             # Create draft MR
glab mr create --reviewer=alice,bob
glab mr create --fill --draft      # Uses commit info and pushes the branch

# Viewing & Managing
glab mr view 123                   # View MR #123
glab mr checkout 123               # Checkout MR branch
glab mr approve 123                # Approve MR
glab mr merge 123                  # Merge MR
glab mr note 123 -m "Comment"      # Add comment
```

## Issues

```zsh
# Listing
glab issue list                    # All issues
glab issue list --assignee=@me     # Assigned to me
glab issue list --label=bug        # With label

# Creating & Managing
glab issue create                  # Interactive
glab issue create --title "Bug" --label=bug
glab issue create --title "Follow-up" --linked-mr 123
glab issue view 456                # View issue
glab issue close 456               # Close issue
```

## CI/CD

```zsh
# Pipelines
glab ci list                       # List pipelines
glab ci status                     # Pipeline status
glab ci trace                      # View logs
glab ci view                       # Interactive pipeline view

# Running & Managing
glab ci run                        # Create or run a new pipeline
glab ci trigger lint               # Trigger a manual job in an existing pipeline
glab ci run-trig --token xxxx      # Run a pipeline trigger token flow
glab ci lint                       # Lint .gitlab-ci.yml
glab ci retry                      # Retry pipeline
glab ci cancel                     # Cancel pipeline
```

## Repository

```zsh
glab repo clone group/subgroup/project  # Clone repository
glab repo view                     # View repo details
glab repo view group/subgroup/project
glab repo fork                     # Fork repository
```

## API

```zsh
glab api projects/:id/merge_requests           # GET request
glab api "projects/:id/issues?per_page=100"   # Keep pagination in query string
glab api --method POST projects/:id/issues \
  --field title="Bug"              # POST with data
```

## Common Flags

```zsh
--help, -h                         # Show help
--repo, -R group/subgroup/project  # Specify repository
--web, -w                          # Open in browser
--output, -o json                  # JSON output
--verbose                          # Verbose output
```

## Environment Variables

```zsh
GITLAB_TOKEN=xxx                   # API token
GITLAB_HOST=gitlab.vi.vector.int   # Workspace default host override
```

## Configuration

```zsh
glab config get                    # View configuration
glab config set key value          # Set config value
```

## Complete Command List

- `glab alias` - Create command shortcuts
- `glab api` - Make API calls
- `glab auth` - Authentication management
- `glab changelog` - Generate changelogs
- `glab check-update` - Check for updates
- `glab ci` - CI/CD operations
- `glab cluster` - Kubernetes cluster management
- `glab completion` - Shell completion
- `glab config` - Configuration management
- `glab deploy-key` - Deploy key management
- `glab duo` - GitLab Duo AI features
- `glab incident` - Incident management
- `glab issue` - Issue tracking
- `glab iteration` - Iteration management
- `glab job` - CI job operations
- `glab label` - Label management
- `glab mr` - Merge request operations
- `glab opentofu` - OpenTofu integration
- `glab release` - Release management
- `glab repo` - Repository operations
- `glab schedule` - Pipeline schedule management
- `glab securefile` - Secure file management
- `glab snippet` - Snippet operations
- `glab ssh-key` - SSH key management
- `glab stack` - Stack management
- `glab token` - Access token management
- `glab user` - User operations
- `glab variable` - CI/CD variable management
- `glab version` - Show version

## Tips

1. Use `glab <command> --help` for detailed help
2. Commands auto-detect repository context from git remote
3. Use `-R group/subgroup/project` or a host-qualified target when outside a repository
4. Most commands have `--web` flag to open in browser
5. Use `--output=json` for scripting
6. Enable completion with `glab completion --shell zsh` in this workspace
