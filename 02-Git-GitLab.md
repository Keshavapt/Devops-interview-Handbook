# 02-Git-GitLab.md

# Git & GitLab Interview Handbook

---

# Git Fundamentals

## What is Git?

Git is a distributed version control system used to track source code changes, collaborate with teams, maintain version history, and support CI/CD workflows.

---

## Git Workflow

```text
Working Directory
      ↓
Staging Area
      ↓
Local Repository
      ↓
Remote Repository
```

Commands:

```bash
git add .
git commit -m "message"
git push origin main
```

---

# Git Architecture

## Working Directory

Files being modified.

Example:

```bash
vim app.py
```

---

## Staging Area

Files selected for commit.

```bash
git add app.py
```

---

## Local Repository

Commit history stored locally.

```bash
git commit -m "Added feature"
```

---

## Remote Repository

GitHub/GitLab/Bitbucket.

```bash
git push origin main
```

---

# Daily Git Commands

## Clone Repository

```bash
git clone <repo-url>
```

Example:

```bash
git clone https://gitlab.com/devops/project.git
```

---

## Check Status

```bash
git status
```

---

## Pull Latest Changes

```bash
git pull origin main
```

---

## Push Changes

```bash
git push origin main
```

---

## View Commit History

```bash
git log
```

Compact:

```bash
git log --oneline
```

---

## Check Difference

```bash
git diff
```

---

# Branching

## Create Branch

```bash
git checkout -b feature/login
```

---

## Switch Branch

```bash
git checkout develop
```

---

## List Branches

```bash
git branch
```

---

## Delete Branch

```bash
git branch -d feature/login
```

---

# Branching Strategies

## Git Flow

```text
main
 │
develop
 │
feature/*
 │
release/*
 │
hotfix/*
```

Best for:

- Enterprise projects
- Release management

---

## Trunk Based Development

```text
main
 │
short-lived feature branches
```

Best for:

- Fast deployments
- CI/CD

---

## Interview Question

### Which branching strategy do you use?

Answer:

At Oracle we primarily used feature branches and merge requests into shared branches. All code changes were validated through GitLab CI/CD pipelines before promotion to higher environments.

---

# Git Merge

## What is Merge?

Combines changes from one branch into another.

```bash
git merge feature/login
```

---

Example:

```text
main
 │
 ├── feature-login
 │
Merge
 │
main
```

---

# Git Rebase

## What is Rebase?

Moves commits to a new base.

```bash
git rebase main
```

---

Example:

Before:

```text
main
 │
 ├── feature
```

After:

```text
main
 ├── feature
```

Linear history.

---

## Merge vs Rebase

| Merge                | Rebase           |
| -------------------- | ---------------- |
| Preserves history    | Rewrites history |
| Creates merge commit | No merge commit  |
| Easier               | Cleaner          |
| Safer                | Riskier          |

---

## Interview Answer

Use Merge for:

- Shared branches

Use Rebase for:

- Local feature cleanup

---

# Cherry Pick

## What is Cherry Pick?

Copies a specific commit.

```bash
git cherry-pick <commit-id>
```

Example:

Move only one bug fix.

---

# Git Reset

## Soft Reset

```bash
git reset --soft HEAD~1
```

Removes commit.

Keeps changes staged.

---

## Mixed Reset

```bash
git reset HEAD~1
```

Keeps changes.

Unstages files.

---

## Hard Reset

```bash
git reset --hard HEAD~1
```

Deletes commit and changes.

Dangerous.

---

# Git Revert

## What is Revert?

Creates new commit that undoes previous commit.

```bash
git revert <commit-id>
```

Safe for production.

---

## Reset vs Revert

| Reset            | Revert              |
| ---------------- | ------------------- |
| Rewrites history | Preserves history   |
| Dangerous        | Safe                |
| Local cleanup    | Production rollback |

---

# Git Stash

## What is Stash?

Temporary storage of uncommitted changes.

```bash
git stash
```

Restore:

```bash
git stash pop
```

---

## Use Case

Need urgent branch switch.

Store current work:

```bash
git stash
git checkout hotfix
```

---

# Git Tags

## What is Tag?

Marks release version.

```bash
git tag v1.0
```

Push tag:

```bash
git push origin v1.0
```

---

# Merge Conflicts

## Why Do They Occur?

Two users modify same lines.

Example:

Developer A:

```python
port=8080
```

Developer B:

```python
port=9090
```

Git cannot decide.

---

## Resolve Conflict

```bash
git status
```

Edit file.

```bash
git add .
git commit
```

---

# PAT (Personal Access Token)

## What is PAT?

Alternative to password authentication.

Used by:

- GitLab
- GitHub

Example:

```bash
git clone https://token@gitlab.com/repo.git
```

---

# Git Fetch vs Pull

## Fetch

```bash
git fetch
```

Downloads changes.

Does not merge.

---

## Pull

```bash
git pull
```

Downloads + merges.

---

## Interview Question

Difference?

Answer:

Fetch is safer because it allows review before merging. Pull automatically merges changes.

---

# Force Push

## Dangerous Command

```bash
git push --force
```

Can overwrite history.

---

## Safer Option

```bash
git push --force-with-lease
```

Protects others' commits.

---

# GitLab Fundamentals

## What is GitLab?

Git repository platform providing:

- Source control
- CI/CD
- Security Scanning
- Package Registry
- Issue Tracking

---

# GitLab Components

## Repository

Stores code.

---

## Merge Request (MR)

Equivalent of Pull Request.

Workflow:

```text
Feature Branch
      ↓
Merge Request
      ↓
Pipeline Validation
      ↓
Code Review
      ↓
Merge
```

---

## GitLab Runner

Executes jobs.

Example:

```text
Build
Test
Deploy
```

Runs on:

- VM
- Docker
- Kubernetes

---

# GitLab Pipeline Structure

Example:

```yaml
stages:
  - build
  - test
  - deploy
```

---

Job:

```yaml
build:
  stage: build
  script:
    - mvn package
```

---

# Shared Templates

## What are Shared Templates?

Reusable pipeline definitions.

Example:

```yaml
include:
  - project: cicd/common
```

Benefits:

- Standardization
- Reusability
- Governance

---

## Oracle Example

Interview Answer:

We maintained centralized GitLab CI/CD templates used across multiple repositories. Security scans, build standards, and deployment logic were inherited from shared templates.

---

# Pipeline Artifacts

## What are Artifacts?

Files generated by pipeline.

Example:

```text
jar
war
reports
logs
```

---

Example:

```yaml
artifacts:
  paths:
    - target/
```

---

# Variables

## Pipeline Variables

Store:

- Tokens
- URLs
- Environment Names

Example:

```yaml
variables:
  ENV: prod
```

---

# GitLab Security

## Protected Branch

Prevent direct push.

Example:

```text
main
master
production
```

---

## Protected Variables

Hide secrets.

Example:

```text
AWS_KEY
TOKEN
PASSWORD
```

---

# Merge Request Validation Flow

```text
Developer Commit
       ↓
Push Feature Branch
       ↓
MR Created
       ↓
Pipeline Triggered
       ↓
Build
       ↓
Unit Test
       ↓
Security Scan
       ↓
Approval
       ↓
Merge
```

---

# Interview Questions

## What happens after git push?

Answer:

1. Push reaches remote repository.
2. GitLab receives webhook event.
3. Pipeline triggers.
4. Build stage executes.
5. Test stage executes.
6. Security scans run.
7. Deployment stage executes if conditions pass.

---

## Explain your GitLab CI/CD architecture.

Answer:

At Oracle, we used centralized GitLab CI/CD templates. Multiple repositories consumed common templates for standardized build, security, testing, and deployment workflows. Security gates such as Fortify, Black Duck, OWASP ZAP, and secret detection were integrated into the pipeline before deployments.

---

## How do you secure GitLab?

Answer:

- Protected branches
- Protected variables
- Merge request approvals
- Security scanning
- Role-based access
- Runner isolation
- Secret management

---

# Git Cheat Sheet

```bash
git clone
git status
git add .
git commit -m
git push
git pull
git fetch
git log
git diff
git checkout
git checkout -b
git merge
git rebase
git stash
git stash pop
git revert
git reset
git cherry-pick
git tag
git branch
```

---

# Top 20 Interview Questions

1. Merge vs Rebase
2. Fetch vs Pull
3. Reset vs Revert
4. Cherry Pick
5. Git Stash
6. Git Tags
7. Merge Conflict
8. Branching Strategies
9. PAT
10. Force Push vs Force-With-Lease
11. Git Workflow
12. GitLab Runner
13. GitLab Pipeline
14. Merge Request
15. Artifacts
16. Shared Templates
17. Protected Branches
18. Protected Variables
19. CI/CD Trigger Flow
20. GitLab Security
