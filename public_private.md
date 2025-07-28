# Git Workflow Description

---

## Initial Workflow: Repository Setup and Creating the Public Branch

### 1. Create Two GitHub Repositories

- **Private repository:** `priv_dataRepo` (set to *private*) — development happens on `master`
- **Public repository:** `dataRepo` (set to *public*) — public branch is called `main`

---

### 2. Push Everything to the Private Repository (`priv_dataRepo`)

Add the private remote and mirror the entire local repo (including all branches, tags, and history):

```


# Add private remote repository

git remote add private git@github.com:YOUR_USERNAME/priv_dataRepo.git

# Mirror push: synchronize all branches, tags, and history

git push --mirror private

# Optional: check or change the default branch on GitHub (Settings tab)

```

---

### 3. Prepare a Clean Branch for the Public Repo

Create an orphan branch named `main` and squash-merge the current `master` state as a single commit:

```


# Create orphan branch main (no history)

git checkout --orphan main

# Optional: if not working from a fresh clone, delete all files

# rm -rf *           \# Warning: deletes all files in working directory!

# Reset to empty state (works only if HEAD exists)

git reset --hard

# If no HEAD exists, create an empty commit as base

git commit --allow-empty -m "Init"

# Squash merge the contents of master into the orphan branch

git merge --squash master --allow-unrelated-histories

# Commit the public snapshot

git commit -m "First public version"

```

> Now `main` contains a single commit with the current project state — no previous history.

---

### 4. Add Public Remote and Push `main` Branch
- **Never merge the private `master` branch directly into the public `main`.** Otherwise, the entire private commit history will become public.
- **Check upfront push**
```


# Add the public remote repository

git remote add public git@github.com:YOUR_USERNAME/dataRepo.git

# If the public repo already contains files (README, LICENSE, etc.), pull to avoid conflicts

git pull public main --allow-unrelated-histories

# Push main branch to the public repository

git push -u public main

```

> The `dataRepo` now contains only this one commit representing your current work state.

---

### 5. Optional: Fetch Public `main` Branch into Private Repository

This step is useful to keep track of public releases also inside the private repo.

```


# Checkout the main branch locally

git checkout main

# Push main branch back to the private repo

git push private main

```

> This way, the `main` branch with public releases exists also in your private repository.

---

## Development Workflow (Private Repository)

### 1. Clone the Private Repository

```

git clone git@github.com:YOUR_USERNAME/priv_dataRepo.git
cd priv_dataRepo

```

---

### 2. Create a New Feature Branch and Develop

```


# Create new branch from master

git checkout -b feature/your-feature-name

# Make changes, add, and commit

git add .
git commit -m "Feature: description of your feature"

```

---

### 3. Merge Feature Branch After Testing and Review

```


# Switch back to master

git checkout master

# Merge feature branch into master

git merge feature/your-feature-name

# Optionally delete feature branch

git branch -d feature/your-feature-name

# Push changes to private repository

git push private master

```

---

## Release-to-Public Workflow (Public `main` Branch)
- **Never merge the private `master` branch directly into the public `main`.** Otherwise, the entire private commit history will become public.
- **Check upfront push**
### 0. Preparation: Create a fresh, separate local clone for the release process

To avoid accidental data loss, it is best to create a **fresh local clone** of the private repository, used exclusively for generating the public release.

```bash
# Create a new folder and clone the private repository into it
git clone git@github.com:YOUR_USERNAME/priv_dataRepo.git release-clone
cd release-clone
```


### 1. Fetch public `main` branch and prepare local release branch

```bash
# Add public remote if not already added
git remote add public git@github.com:YOUR_USERNAME/dataRepo.git

# Fetch the current state of the public main branch
git fetch public main

# Create local branch main tracking public/main
git checkout -b main public/main
```


### 2. Switch to `main` and create a clean public release commit

> In this separate clone, you can safely delete all files without risking your main working directory.

```bash
# Ensure you are on main branch
git checkout main

# Delete all files in the working directory to detect deletions correctly
rm -rf *

# Squash-merge the private master branch as a single commit
git merge --squash master

# Commit with a clear release description
git commit -m "Public Release: description of the release"
```


### 3. Push the public release to the public repository
- **Never merge the private `master` branch directly into the public `main`.** Otherwise, the entire private commit history will become public.
- **Check upfront push**
```bash
git push public main
```


### 4. Optional: Push the public `main` branch back to the private repository

```bash
git checkout main
git push private main
```

> This keeps the public release history visible and trackable inside the private repository as well.

**Summary:**

- Use a **separate fresh local clone** for the release process to keep your main working directory safe.
- There, you can safely run `rm -rf *` without risking important data.
- This ensures that deletions and other changes are properly reflected in the public branch.

# Important Notes

- **Never merge the private `master` branch directly into the public `main`.** Otherwise, the entire private commit history will become public.
- Always use `git merge --squash` when merging to public `main` to create a single commit representing the release.
- Replace all occurrences of `YOUR_USERNAME` with your actual GitHub username.
- Replace dataRepo and priv_dataRepo with the actual names

