## Branching Strategies
Git branching is one of its core features, and mastering it can drastically improve your workflow. Here are some key strategies:

### Git Flow
- Git Flow is a popular branching model used in software development that involves using specific branches for different stages of development.
- The key branches are:
  - main (or master) – This is where the stable version of the code lives. It should always be deployable.
  - develop – This is where feature branches are merged, and the integration of new features happens.
  - feature branches – These are used for individual features, and they are merged into develop once the feature is completed.
  - release branches – These are used to prepare new releases, where the focus is on bug fixing and final adjustments.
  - hotfix branches – These are used to quickly fix issues in the main branch.

### How to set up Git Flow:
- Install Git Flow:
```bash
sudo apt-get install git-flow
```
- Initialize Git Flow in your repository:
```bash
git flow init
```
- Create feature, release, and hotfix branches:
```bash
git flow feature start my-feature
git flow release start 1.0.0
git flow hotfix start fix-issue
```

## Rebasing (vs. Merging)
Understanding the difference between git merge and git rebase can be a game-changer for your workflow.

`git merge`
- Combines two branches (e.g., a feature branch into main).
- Creates a merge commit to record the merging of branches.
- Can lead to a messy history with lots of merge commits.

`git rebase`
- Instead of creating a merge commit, it reapplies your changes on top of another branch, making the history linear.
- Helps keep the Git history cleaner and easier to follow.
- Important: Rebase rewrites commit history, so it’s better used on local branches that haven’t been pushed to a remote.

### How to rebase:
```bash
# Check out your feature branch
git checkout feature-branch

# Rebase onto the latest main branch
git fetch origin
git rebase origin/main
```
### Resolving conflicts during a rebase:
If conflicts occur during a rebase:
1. Resolve the conflict in the affected files.
2. Add the resolved files: `git add <file>`
3. Continue the rebase: `git rebase --continue`
4. Once finished, you can push your rebased changes.

## Advanced Git Commands

### Interactive Rebase (`git rebase -i`)
Interactive rebasing allows you to edit, squash, or reorder commits. This is very useful for cleaning up commit history before pushing.
```bash
# Rebase interactively for the last 3 commits
git rebase -i HEAD~3
```
The interactive rebase editor lets you choose:
- `pick`: Keep the commit as it is.
- `squash`: Combine the commit with the previous one.
- `edit`: Edit the commit (e.g., to change the commit message).

### Stashing (`git stash`)
If you’re in the middle of some changes but need to switch branches or work on something else, you can use `git stash` to temporarily store your changes.
```bash
# Save changes to the stash
git stash

# List stashed changes
git stash list

# Apply the most recent stash
git stash apply

# Apply and remove the stash from the list
git stash pop
```

### Git Bisect
Git bisect helps you find the commit that introduced a bug by binary searching through your commit history. This can be very helpful when debugging.
```bash
# Start bisecting
git bisect start

# Mark good commit (a commit where the bug is not present)
git bisect good <commit>

# Mark bad commit (a commit where the bug is present)
git bisect bad <commit>

# Git will automatically check out commits between "good" and "bad"
```
You can use `git bisect` to narrow down the commit range until you find the exact commit that introduced the issue.

## Tagging
Git tags are used to mark specific points in history as important (e.g., releases).

### Creating tags:
```bash
# Create a lightweight tag
git tag v1.0.0

# Create an annotated tag with a message
git tag -a v1.0.0 -m "First release"
```
### Pushing tags to remote:
```bash
git push origin v1.0.0
```

### Listing tags:
```bash
git tag
```

### Deleting a tag:
```bash
git tag -d v1.0.0
```

## Git Hooks
Git hooks allow you to run scripts at certain points in the Git lifecycle. For example, you can set up a pre-commit hook to run tests before committing.

### To set up a hook:
1. Go to your `.git/hooks` directory.
2. Copy the sample hook (e.g., `pre-commit.sample`) and rename it to `pre-commit`.
3. Make the script executable (`chmod +x pre-commit`) and add your custom logic.

Example `pre-commit` hook:
```bash
#!/bin/sh
# Run tests before committing
python -m unittest discover > result.log || { echo "Tests failed!"; exit 1; }
```

## Collaborating with Git

### Forking & Pull Requests (PRs)
GitHub makes collaboration easy with forks and pull requests:
- Fork a repository to create your own copy of someone else’s project.
- Clone your fork locally.
- Create a branch for your changes.
- Submit a pull request from your branch to the original repository for review.

### Squash Merging:
Instead of keeping a messy history of all your feature commits, you can squash them into a single commit when merging a pull request. This makes the history cleaner and more readable.

## GitHub Actions (CI/CD)
GitHub Actions is an amazing tool to automate your workflows directly on GitHub. You can set up continuous integration (CI), continuous delivery (CD), and other automated workflows to run tests, deploy applications, etc.

### Creating a simple workflow:
Create a `.github/workflows/ci.yml` file in your repo:
```yaml
name: CI
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
      - name: Run tests
        run: |
          python -m unittest discover
```
This will run every time you push code to the repository, ensuring that your code passes tests before being merged.