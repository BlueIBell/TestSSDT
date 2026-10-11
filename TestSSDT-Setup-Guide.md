# TestSSDT: Repo Setup Guide (main + develop for a small team)

This guide creates a GitHub repository named **TestSSDT** with two long-lived branches:

- `main`: stable, release-ready code.
- `develop`: integration branch where team members merge their feature work.

Workflow: `feature/*` → `develop` → `main`

---

## 1. Create the repository

1. On GitHub, click **+ → New repository**.
2. **Repository name:** `TestSSDT`
3. Choose **Public** or **Private**.
   - Note: on a free plan, rulesets are only enforced on **public** repos.
4. Tick **Add a README file** (this creates the `main` branch).
5. Click **Create repository**.

## 2. Create the `develop` branch

1. Open the repo's **Code** tab.
2. Click the branch dropdown (shows `main`).
3. Type `develop`.
4. Click **Create branch: develop from main**.

You should now see **2 Branches**.

## 3. Choose the default branch (optional)

By default `main` is the default branch. To make `develop` the default instead (so pull requests target it automatically):

1. Go to **Settings → General → Default branch**.
2. Click the switch icon next to `main`, choose `develop`, and click **Update**.

Trade-offs:

| Default = `develop` | Default = `main` |
|---|---|
| PRs target `develop` automatically | Visitors see stable, released code |
| `git clone` checks out `develop` | Conventional; matches tool defaults |
| Issues are not auto-closed by merges into `develop` | `Fixes #12` closes issues when merged to `main` |

## 4. Add the team

1. Go to **Settings → Collaborators → Add people**.
2. Enter each teammate's GitHub username or email.
3. Give them **Write** access.

They accept the invitation, then clone the repo directly (no forks needed).

## 5. Protect `develop`

1. Go to **Settings → Branches → Add branch ruleset**.
2. **Ruleset name:** `protect-develop`
3. **Enforcement status:** **Active**
4. **Bypass list:** leave empty (rules apply to everyone), or add the **Repository admin** role if you want to push directly yourself.
5. **Target branches:** **Add target → Include by pattern**, enter `develop`, and click **Add Inclusion pattern**.
6. Under **Branch rules**, tick:
   - **Restrict deletions**
   - **Block force pushes**
   - **Require a pull request before merging**
7. Leave **Required approvals** at **0** for speed.
8. Click **Create**.

## 6. Protect `main`

Repeat step 5 with these changes:

- **Ruleset name:** `protect-main`
- **Target pattern:** `main`
- **Required approvals:** **1** (recommended for release code)

> If you work alone, keep approvals at 0. You cannot approve your own pull request.

---

## 7. Daily workflow for each team member

### Get set up (once)

```bash
git clone https://github.com/<OWNER>/TestSSDT.git
cd TestSSDT
git switch develop
```

### Start a feature

```bash
git switch develop
git pull
git switch -c feature/my-feature
```

### Commit and push

```bash
git add .
git commit -m "Describe the change"
git push -u origin feature/my-feature
```

### Open a pull request

1. On GitHub, click **Compare & pull request**.
2. Set **base: `develop`** and **compare: `feature/my-feature`**.
3. Request a review from a teammate and merge once approved.
4. Use **Squash and merge** to keep history clean.
5. Delete the feature branch after merging.

### Clean up locally

```bash
git switch develop
git pull
git branch -d feature/my-feature
```

---

## 8. Release: `develop` → `main`

When `develop` holds a stable set of changes:

1. Open a pull request with **base: `main`** and **compare: `develop`**.
2. Get the required approval.
3. Merge using a **regular merge commit** (not squash), so the branches don't diverge.
4. Optionally tag the release:

```bash
git switch main
git pull
git tag v1.0.0
git push origin v1.0.0
```

## 9. Hotfixes (urgent production fixes)

```bash
git switch main
git pull
git switch -c hotfix/fix-description
```

1. Open a PR into `main` and merge it.
2. Merge `main` back into `develop` so the fix isn't lost.

---

## Branch naming conventions

| Prefix | Use for |
|---|---|
| `feature/` | New functionality |
| `bugfix/` | Non-urgent bug fixes |
| `hotfix/` | Urgent fixes from `main` |

## Quick reference

| Task | Command |
|---|---|
| List branches | `git branch -a` |
| Switch branch | `git switch <name>` |
| Create and switch | `git switch -c <name>` |
| Update current branch | `git pull` |
| Push new branch | `git push -u origin <name>` |
| Delete local branch | `git branch -d <name>` |
