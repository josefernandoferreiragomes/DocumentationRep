# GitHub Commands Quick Reference

## Account & Authentication

### Configure Git Identity
```bash
git config --global user.name "John Doe"
git config --global user.email "john@example.com"
```

### Authenticate with GitHub CLI
```bash
gh auth login
```

### Check Authentication Status
```bash
gh auth status
```

### Logout
```bash
gh auth logout
```

---

## Repository Management

### Create a New GitHub Repository
```bash
gh repo create my-project
```

Interactive mode:
```bash
gh repo create
```

### Create a Private Repository
```bash
gh repo create my-project --private
```

### Create a Repository from Current Folder
```bash
gh repo create my-project --source=. --push
```

### Clone a Repository
```bash
gh repo clone owner/repository
```

Example:
```bash
gh repo clone microsoft/vscode
```

### Fork a Repository
```bash
gh repo fork owner/repository
```

### Open Repository in Browser
```bash
gh repo view --web
```

### View Repository Information
```bash
gh repo view
```

### Delete a Repository
```bash
gh repo delete owner/repository
```

---

## Branch Operations

### Create and Switch to New Branch
```bash
git checkout -b feature/new-feature
```

### Push New Branch to GitHub
```bash
git push -u origin feature/new-feature
```

### List Remote Branches
```bash
git branch -r
```

### Delete Remote Branch
```bash
git push origin --delete feature/old-feature
```

### Rename Current Branch
```bash
git branch -m new-branch-name
```

### Set Upstream Branch
```bash
git push --set-upstream origin branch-name
```

---

## Pull Requests

### Create Pull Request
```bash
gh pr create
```

### Create Pull Request with Title and Description
```bash
gh pr create --title "Add authentication module" --body "Implements JWT authentication"
```

### List Pull Requests
```bash
gh pr list
```

### View Pull Request Details
```bash
gh pr view 15
```

### Checkout Pull Request Locally
```bash
gh pr checkout 15
```

### Merge Pull Request
```bash
gh pr merge 15
```

### Close Pull Request
```bash
gh pr close 15
```

### Open Pull Request in Browser
```bash
gh pr view 15 --web
```

---

## Issues

### List Issues
```bash
gh issue list
```

### Create Issue
```bash
gh issue create
```

### View Issue
```bash
gh issue view 10
```

### Close Issue
```bash
gh issue close 10
```

### Reopen Issue
```bash
gh issue reopen 10
```

### Assign Issue
```bash
gh issue edit 10 --add-assignee username
```

### Add Labels to Issue
```bash
gh issue edit 10 --add-label bug
```

---

## Releases & Tags

### Create Git Tag
```bash
git tag v1.0.0
```

### Push Tag to GitHub
```bash
git push origin v1.0.0
```

### Push All Tags
```bash
git push --tags
```

### Create GitHub Release
```bash
gh release create v1.0.0
```

### Create Release with Notes
```bash
gh release create v1.0.0 --notes "Initial stable release"
```

### List Releases
```bash
gh release list
```

### Download Release Assets
```bash
gh release download v1.0.0
```

---

## Actions & CI/CD

### List GitHub Actions Workflows
```bash
gh workflow list
```

### View Workflow Runs
```bash
gh run list
```

### Watch Workflow Execution
```bash
gh run watch
```

### View Workflow Logs
```bash
gh run view
```

### Re-run Failed Workflow
```bash
gh run rerun RUN_ID
```

### Trigger Workflow Manually
```bash
gh workflow run workflow.yml
```

---

## Collaboration

### Add Collaborator
```bash
gh repo add-collaborator username
```

### List Repository Collaborators
```bash
gh api repos/OWNER/REPO/collaborators
```

### Review Pull Request
```bash
gh pr review 15 --approve
```

### Request Changes in Pull Request
```bash
gh pr review 15 --request-changes --body "Please fix validation issues"
```

### Comment on Pull Request
```bash
gh pr comment 15 --body "Looks good overall"
```

---

## GitHub Codespaces

### List Codespaces
```bash
gh codespace list
```

### Create Codespace
```bash
gh codespace create
```

### SSH into Codespace
```bash
gh codespace ssh
```

### Stop Codespace
```bash
gh codespace stop
```

### Delete Codespace
```bash
gh codespace delete
```

---

## GitHub Gists

### Create Gist
```bash
gh gist create file.txt
```

### Create Public Gist
```bash
gh gist create file.txt --public
```

### List Gists
```bash
gh gist list
```

### Clone Gist
```bash
gh gist clone GIST_ID
```

---

## Search Operations

### Search Repositories
```bash
gh search repos "asp.net core"
```

### Search Issues
```bash
gh search issues "bug label:backend"
```

### Search Pull Requests
```bash
gh search prs "authentication fix"
```

### Search Code
```bash
gh search code "IAppraisalRepository"
```

---

## Useful Advanced Commands

### View API Rate Limits
```bash
gh api rate_limit
```

### Execute Direct GitHub API Request
```bash
gh api repos/OWNER/REPO
```

### Create Repository Secret
```bash
gh secret set SECRET_NAME
```

### List Repository Secrets
```bash
gh secret list
```

### Upload Release Asset
```bash
gh release upload v1.0.0 app.zip
```

---

# Typical Workflows

## Workflow: Create Project and Push Initial Code

### 1. Initialize Git
```bash
git init
```

### 2. Add Files
```bash
git add .
```

### 3. Commit
```bash
git commit -m "Initial commit"
```

### 4. Create GitHub Repository
```bash
gh repo create my-project --public --source=. --push
```

---

## Workflow: Feature Branch Development

### 1. Create Branch
```bash
git checkout -b feature/payment-module
```

### 2. Commit Changes
```bash
git add .
git commit -m "Add payment service"
```

### 3. Push Branch
```bash
git push -u origin feature/payment-module
```

### 4. Create Pull Request
```bash
gh pr create
```

---

## Workflow: Release Deployment

### 1. Create Tag
```bash
git tag v2.0.0
```

### 2. Push Tag
```bash
git push origin v2.0.0
```

### 3. Create GitHub Release
```bash
gh release create v2.0.0 --generate-notes
```

---

# Common Troubleshooting

## Authentication Failed
Re-authenticate:
```bash
gh auth login
```

## Remote Already Exists
```bash
git remote remove origin
git remote add origin https://github.com/user/repo.git
```

## Pull Request Merge Conflicts
Fetch latest changes:
```bash
git pull origin main
```

Resolve conflicts manually, then:
```bash
git add .
git commit
```

## Undo Last Commit (Keep Changes)
```bash
git reset --soft HEAD~1
```

## Remove Last Commit Completely
```bash
git reset --hard HEAD~1
```

---

# Install GitHub CLI

## Windows
Using Winget:
```powershell
winget install GitHub.cli
```

Using Chocolatey:
```powershell
choco install gh
```

## macOS
Using Homebrew:
```bash
brew install gh
```

## Linux (Ubuntu/Debian)
```bash
sudo apt install gh
```

---

# Best Practices

- Use feature branches for all development work.
- Create pull requests instead of pushing directly to `main`.
- Use semantic versioning (`v1.0.0`).
- Protect main branches in GitHub settings.
- Use GitHub Actions for CI/CD pipelines.
- Write meaningful commit messages.
- Review pull requests before merging.
- Store secrets in GitHub Secrets instead of source code.

---

# References

## Official Documentation

- GitHub CLI Documentation: https://cli.github.com/manual/
- GitHub Docs: https://docs.github.com/en
- Git Documentation: https://git-scm.com/doc

## Recommended Learning Resources

- GitHub Skills: https://skills.github.com/
- GitHub Actions Documentation: https://docs.github.com/en/actions
- GitHub Flow Guide: https://docs.github.com/en/get-started/using-github/github-flow
