# Git Programming Guide

## Commit Message Style

```yaml
id: git.commit-message-style
tier: convention
review_passes: [structural]
summary: Before creating a commit, inspect recent history and match the repository's commit-message style.
applies_when:
  constructs: [git, commit, commit-message, repository-history]
```

Before creating a commit, inspect recent repository history with `git log --oneline` and match the local commit-message style.

When the repository uses a structured prefix, preserve that structure instead of inventing a generic message. For example, if recent commits use `[scope] Kind: summary`, choose the appropriate scope and kind from the existing history.

## Branch Policy

```yaml
id: git.branch-policy
tier: convention
review_passes: [structural]
summary: Use main or master for production, staging for the next regular release, and typed work branches such as fix, ref, and ft.
applies_when:
  constructs: [git, branch, production, staging, release, hotfix, feature, refactor, bugfix]
```

For deployable products, `main` or `master` represents production code. It should point to the code currently live in production after deployment.

Use typed work branches where the first path component describes the branch intent:

- `fix/...` for production bug fixes.
- `ref/...` for refactors.
- `ft/...` for features.

Use `staging` for work selected for the next regular release. Treat `staging` as an early integration and testing branch for the upcoming regular release, not as a dumping ground for all active work.

Keep work that is not planned for the next regular release on its own typed work branch. Regular release branches should include `staging`. Hotfix branches may include `staging` when appropriate, but are not required to.

Do not assume a default `dev` branch unless the repository explicitly documents one.

## Merge Policy

```yaml
id: git.merge-policy
tier: convention
review_passes: [structural]
summary: Integrate public work branches with merge commits; reserve rebase and squash for local cleanup only.
applies_when:
  constructs: [git, merge, rebase, squash, branch, public-branch, staging, release, hotfix]
```

When integrating feature, refactor, or fix branches into `staging`, release branches, hotfix branches, `main`, or `master`, use normal merges. Do not replace public branch integration with squash merges or rebases.

Treat squash and rebase as personal local-history cleanup tools only, before commits are pushed to a public repository or shared with other people. In normal repository work, prefer merge commits for public branch integration.
