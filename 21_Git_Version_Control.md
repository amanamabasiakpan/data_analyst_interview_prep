# 21. Git & Version Control

## Theoretical Questions

### Q1: What is Git and why do data analysts need it?
**A:** Git is a distributed version control system that tracks changes to files over time. Data analysts need it because: it enables collaboration on code (SQL, Python, notebooks), provides a history of analysis changes, allows safe experimentation via branches, supports code review, and is essential for reproducible data science. Most data teams use Git for ETL code, analysis scripts, dashboards-as-code, and documentation.

### Q2: What is the difference between Git and GitHub?
**A:**
- **Git:** The version control software itself — runs locally, tracks changes, manages branches. Open-source, created by Linus Torvalds.
- **GitHub:** A cloud-based platform that hosts Git repositories, adds collaboration features (pull requests, issues, actions), and provides a web interface. Alternatives: GitLab, Bitbucket, Azure DevOps.
Git is the engine; GitHub is a service built on top of it.

### Q3: What is a Git repository and how do you create one?
**A:** A repository (repo) is a directory where Git tracks file changes. Creation:
- **New local repo:** `git init` in a directory
- **Clone existing:** `git clone <url>` copies a remote repo locally
- **GitHub:** Create via web UI, then clone locally
A repo contains: working files, a `.git` directory (history and metadata), and optionally remote connections.

### Q4: What is the Git workflow (add, commit, push, pull)?
**A:**
1. **Modify:** Edit files in your working directory
2. **Stage:** `git add <file>` — marks changes to be included in next commit
3. **Commit:** `git commit -m "message"` — saves a snapshot of staged changes locally with a message describing what changed
4. **Push:** `git push origin main` — uploads local commits to remote repository
5. **Pull:** `git pull origin main` — downloads and merges remote changes to your local branch

### Q5: What is a branch in Git and why is it useful?
**A:** A branch is an independent line of development. The default is usually `main` or `master`. Branches let you:
- Work on features without affecting stable code
- Experiment safely (try a new analysis approach)
- Collaborate (multiple people work on different features simultaneously)
- Create pull requests for code review before merging
Common branch names: `feature/new-dashboard`, `bugfix/data-pipeline`, `experiment/ml-model`

### Q6: What is a merge conflict and how do you resolve it?
**A:** A conflict occurs when two branches modify the same part of the same file, and Git can't automatically reconcile the changes. Resolution:
1. Git marks conflicted files with `<<<<<<< HEAD` (your changes), `=======` (separator), `>>>>>>> branch-name` (their changes)
2. Open the file, decide which changes to keep (or combine both)
3. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
4. Stage the resolved file: `git add <file>`
5. Complete the merge: `git commit`
**Prevention:** Pull frequently, communicate with teammates, and make small, focused commits.

### Q7: What is a pull request (PR) and what is its purpose?
**A:** A pull request is a request to merge changes from one branch into another (typically feature branch → main). Purpose:
- **Code review:** Teammates review changes before merging
- **Discussion:** Comment on specific lines, suggest improvements
- **CI/CD integration:** Automated tests run before merge
- **Documentation:** PR description explains what changed and why
- **Approval workflow:** Required approvals before merging to main

### Q8: What is the difference between `git merge` and `git rebase`?
**A:**
- **`git merge`:** Creates a new commit that combines two branches. Preserves history but creates "merge commits" that can make history look messy.
- **`git rebase`:** Moves your branch's commits to the tip of another branch. Creates a linear history (cleaner) but rewrites commit history. **Never rebase commits that have been pushed to a shared branch** — it changes commit hashes and confuses collaborators.
**Rule:** Rebase your local feature branch before merging; merge (don't rebase) shared branches like main.

### Q9: What is `git stash` and when do you use it?
**A:** Temporarily saves uncommitted changes without committing them. Use when:
- You need to quickly switch branches but have unfinished work
- You want to pull latest changes but have local modifications
- You need to test something on a clean branch
Commands: `git stash` (save), `git stash pop` (restore), `git stash list` (view), `git stash drop` (delete)

### Q10: What is `.gitignore` and what should data analysts include in it?
**A:** A file that tells Git which files/directories to ignore. Data analysts should ignore:
- **Data files:** `*.csv`, `*.xlsx`, `*.parquet`, `*.db` (never commit data to Git)
- **Credentials:** `.env`, `config.json` with API keys, `secrets.yml`
- **Environment:** `venv/`, `__pycache__/`, `.ipynb_checkpoints/`, `node_modules/`
- **Outputs:** `*.png`, `*.pdf`, `reports/` (generated files)
- **Large files:** Git has a 100MB limit; use Git LFS for large files if needed

### Q11: What is Git LFS and when do you need it?
**A:** Git Large File Storage — an extension for versioning large files (datasets, models, images) without bloating the repository. Instead of storing the file in Git, it stores a pointer and uploads the file to a separate server. Use when: you need to version files >100MB (e.g., trained ML models, large datasets, presentations). Commands: `git lfs track "*.parquet"`, `git lfs push`.

### Q12: What is a commit message best practice?
**A:**
- **Format:** `type: subject` (e.g., `feat: add churn prediction model`, `fix: correct revenue calculation`)
- **Subject:** Imperative mood, 50 chars or less ("Add feature" not "Added feature")
- **Body:** Explain what and why (not how — the code shows how), wrap at 72 chars
- **Types:** feat, fix, docs, style, refactor, test, chore
- **Reference:** Include issue/ticket numbers: `feat: add cohort analysis (#42)`
Good messages help teammates (and future you) understand why changes were made.

### Q13: What is `git log` and how do you use it effectively?
**A:** Views commit history. Useful options:
- `git log --oneline` — compact view, one line per commit
- `git log --graph --decorate --all` — visual branch graph
- `git log --author="name"` — filter by author
- `git log --since="2 weeks ago"` — time-based filter
- `git log --grep="keyword"` — search commit messages
- `git log -p <file>` — show changes to a specific file

### Q14: What is `git diff` and when do you use it?
**A:** Shows differences between commits, branches, or working directory. Use cases:
- `git diff` — unstaged changes in working directory
- `git diff --staged` — changes staged for next commit
- `git diff branch1 branch2` — differences between branches
- `git diff HEAD~1` — changes in the last commit
Essential for reviewing your own changes before committing and understanding what changed in a PR.

### Q15: What is `git reset` and what are the three modes?
**A:** Moves the current branch HEAD to a different commit. Modes:
- **`--soft`:** Moves HEAD but keeps changes staged. Use: undo commit but keep changes ready to recommit.
- **`--mixed` (default):** Moves HEAD and unstages changes (keeps in working directory). Use: undo commit and unstage.
- **`--hard`:** Moves HEAD and discards all changes. **Dangerous** — permanently deletes uncommitted work. Use with extreme caution.

### Q16: What is a remote in Git and how do you manage multiple remotes?
**A:** A remote is a version of your repository hosted elsewhere (GitHub, GitLab, etc.). Commands:
- `git remote -v` — list remotes
- `git remote add origin <url>` — add a remote
- `git remote add upstream <url>` — add upstream (for forks)
- `git fetch upstream` — download upstream changes without merging
- `git push origin feature-branch` — push to your fork
Multiple remotes are common when you fork a repo: `origin` (your fork) and `upstream` (original repo).

### Q17: What is a fork in Git/GitHub and when do you use it?
**A:** A fork is your personal copy of someone else's repository. Changes in your fork don't affect the original. Use when:
- Contributing to open-source projects (fork → change → PR to original)
- Making experimental changes without affecting the main repo
- Creating a starting point for your own project based on another
Forks maintain a connection to the original for easy PR creation.

### Q18: What is `git cherry-pick` and when is it useful?
**A:** Applies a specific commit from one branch to another without merging the entire branch. Useful when:
- A bug fix was committed to the wrong branch and needs to be on main
- You want to apply a hotfix from main to a release branch
- You need one specific change, not an entire branch's history
Command: `git cherry-pick <commit-hash>`

### Q19: What is `git blame` and how do data analysts use it?
**A:** Shows who last modified each line of a file and when. Use cases:
- "Who changed this calculation and why?"
- Understanding the history of a specific line of code
- Finding when a bug was introduced
- Attribution and accountability
Command: `git blame <file>` or `git blame -L 10,20 <file>` for specific lines

### Q20: What is a Git hook and what are common use cases?
**A:** Scripts that run automatically at certain points in the Git workflow. Stored in `.git/hooks/`. Common hooks:
- **pre-commit:** Run linters, formatters, or tests before allowing a commit
- **pre-push:** Run test suite before pushing to remote
- **commit-msg:** Validate commit message format
- **post-merge:** Run `npm install` or `pip install` after pulling
Useful for: enforcing code quality, preventing secrets from being committed, and automating repetitive tasks.

### Q21: What is the Gitflow workflow?
**A:** A branching model with specific branch types:
- **`main`:** Production-ready code
- **`develop`:** Integration branch for features
- **`feature/*`:** Individual features branched from develop
- **`release/*`:** Prepare for production release
- **`hotfix/*`:** Emergency fixes branched from main
**Use when:** You have scheduled releases, multiple features in development, and a need for strict release management. **Alternative:** Trunk-based development (simpler, feature flags) is increasingly popular for CI/CD.

### Q22: What is trunk-based development and how does it compare to Gitflow?
**A:** All developers commit to a single main branch (trunk) frequently (multiple times per day). Features are hidden behind feature flags until ready. Compared to Gitflow:
- **Pros:** Simpler, faster integration, fewer merge conflicts, better for CI/CD
- **Cons:** Requires discipline, feature flags add complexity, less suitable for long-running features
**Modern preference:** Trunk-based for most teams; Gitflow for teams with strict release cycles.

### Q23: What is `git tag` and how is it used in data projects?
**A:** Tags mark specific commits as important (usually releases). Types:
- **Lightweight:** Just a named reference to a commit
- **Annotated:** Full object with tagger name, date, message, and GPG signature
In data projects: tag model versions (`v1.2.0`), dataset versions, or analysis milestones. Example: `git tag -a v2.0 -m "Churn model v2.0 with new features"`

### Q24: What is a Git submodule and when do you use it?
**A:** A Git repository inside another Git repository. Used when:
- A project depends on another repo that is independently maintained
- You want to include shared code/libraries without copying files
- You need to pin to a specific version of the dependency
Caution: Adds complexity. In data projects, often better to use package managers (pip, conda) instead.

### Q25: What is `git bisect` and when is it useful?
**A:** A binary search through commit history to find which commit introduced a bug. You mark a "good" commit (before bug) and "bad" commit (after bug), and Git checks out commits in between, letting you test each one. Efficient for finding bugs in long histories. Command: `git bisect start`, `git bisect bad`, `git bisect good <commit>`.

### Q26: What is the difference between `git fetch` and `git pull`?
**A:**
- **`git fetch`:** Downloads changes from remote but doesn't merge them into your working branch. Safe — lets you review changes before integrating.
- **`git pull`:** `fetch` + `merge` in one command. Downloads and immediately merges remote changes. Can cause merge conflicts.
**Best practice:** `git fetch` first, review what changed (`git log HEAD..origin/main`), then `git merge` or `git rebase` intentionally.

### Q27: What is `git revert` and how does it differ from `git reset`?
**A:**
- **`git revert <commit>`:** Creates a new commit that undoes the changes from a specific commit. Safe for shared history because it doesn't rewrite history.
- **`git reset <commit>`:** Moves branch pointer to a previous commit, optionally discarding changes. Rewrites history — dangerous for shared branches.
**Rule:** Use `revert` for public/shared branches; use `reset` only for local, unshared commits.

### Q28: What is a `.gitattributes` file and why might data analysts need it?
**A:** Specifies attributes for paths in the repo. Use cases for data teams:
- Mark CSV files as non-diffable (they're data, not code): `*.csv binary`
- Specify line endings: `* text=auto` (prevents Windows/Unix line ending issues)
- Merge strategies for specific files: `*.sql merge=union` (keep both versions)
- Linguist overrides for GitHub language detection

### Q29: What is GitHub Actions and how do data teams use it?
**A:** GitHub's CI/CD platform that automates workflows based on Git events. Data team use cases:
- **Automated testing:** Run pytest on every PR
- **Linting:** Check SQL style with sqlfluff, Python with black/flake8
- **Documentation:** Auto-generate docs from docstrings
- **Data pipelines:** Trigger ETL jobs on schedule or on push
- **Deployment:** Deploy dashboards or models when code changes
Defined in `.github/workflows/*.yml` files in the repo.

### Q30: What is a GitHub Issue and how do data teams use them?
**A:** A tracking item for bugs, features, or tasks. Data team workflows:
- **Bug reports:** "Revenue calculation is double-counting returns"
- **Feature requests:** "Add cohort analysis to customer dashboard"
- **Tasks:** "Migrate sales pipeline from CSV to API source"
- **Documentation:** "Update data dictionary for dim_customer"
Linked to PRs with keywords: `Fixes #42` in PR description auto-closes the issue when merged.

## Scenario-Based Questions

### Q31: You accidentally committed a file with API keys. How do you remove it from Git history?
**A:**
1. **Rotate the API key immediately** — it's compromised
2. **Remove from current commit:** `git rm --cached file-with-secrets` + add to `.gitignore`
3. **Remove from history:** `git filter-repo --path file-with-secrets --invert-paths` (modern, recommended) or `git filter-branch --force --index-filter "git rm --cached --ignore-unmatch file-with-secrets" HEAD`
4. **Force push:** `git push origin --force --all` (coordinate with team first!)
5. **Alternative for GitHub:** Use GitHub's secret scanning and automated removal
6. **Prevention:** Use pre-commit hooks with `detect-secrets` or `git-secrets`

### Q32: You're working on a feature branch and the main branch has moved forward with important changes. How do you incorporate them?
**A:**
```bash
# Option 1: Rebase (cleaner history, for local branches)
git checkout feature-branch
git fetch origin
git rebase origin/main
# Resolve any conflicts, then force push: git push --force-with-lease

# Option 2: Merge (safer for shared branches)
git checkout feature-branch
git fetch origin
git merge origin/main
# Resolve conflicts, regular push: git push origin feature-branch

# Best practice: Rebase before creating PR; merge main into feature if PR is already open
```

### Q33: Two team members edited the same SQL file and there's a merge conflict. Walk through resolution.
**A:**
```bash
# After git pull or merge attempt, Git shows:
# CONFLICT (content): Merge conflict in analysis/revenue.sql

# Open the file — you'll see:
# <<<<<<< HEAD
# SELECT SUM(revenue) FROM sales;
# =======
# SELECT SUM(revenue * 1.1) FROM sales;  -- tax adjustment
# >>>>>>> feature/tax-adjustment

# Resolution steps:
# 1. Discuss with the other developer: was the tax adjustment intentional?
# 2. Combine both changes if needed:
#    SELECT SUM(revenue * (1 + tax_rate)) FROM sales;
# 3. Remove conflict markers (<<<<<<<, =======, >>>>>>>)
# 4. Test the SQL to ensure it runs correctly
# 5. Stage: git add analysis/revenue.sql
# 6. Complete: git commit -m "Merge: incorporate tax adjustment into revenue calculation"
```

### Q34: You need to collaborate on a Jupyter notebook with Git. What are the challenges and solutions?
**A:**
**Challenges:**
- Notebooks are JSON files with metadata, outputs, and execution counts — diffs are unreadable
- Large output cells (images, tables) bloat the repo
- Merge conflicts are nearly impossible to resolve manually

**Solutions:**
1. **nbstripout:** Pre-commit hook to clear outputs before committing
2. **jupytext:** Save notebooks as `.py` or `.md` (clean diffs), convert to `.ipynb` on open
3. **ReviewNB:** GitHub integration for readable notebook diffs in PRs
4. **nbdime:** Tool for diffing and merging notebooks
5. **Best practice:** Keep notebooks for exploration; move production code to `.py` files

### Q35: Your team needs to version control both code and SQL queries. How do you structure the repo?
**A:**
```
data-team-repo/
├── README.md
├── .gitignore
├── requirements.txt
├── dbt/
│   ├── models/
│   │   ├── staging/
│   │   ├── marts/
│   │   └── sources.yml
│   ├── tests/
│   └── dbt_project.yml
├── analyses/
│   ├── churn_analysis.ipynb
│   └── cohort_analysis.ipynb
├── scripts/
│   ├── etl/
│   │   ├── extract_sales.py
│   │   └── transform_customers.py
│   └── utils/
│       └── database.py
├── reports/
│   ├── monthly_dashboard.py
│   └── quarterly_review.sql
├── docs/
│   ├── data_dictionary.md
│   └── onboarding.md
└── .github/
    └── workflows/
        └── ci.yml
```

### Q36: A critical bug is in production. You need to fix it immediately. What's your Git workflow?
**A:**
```bash
# 1. Create hotfix branch from main (production)
git checkout main
git pull origin main
git checkout -b hotfix/fix-revenue-bug

# 2. Fix the bug, test thoroughly
# 3. Commit the fix
git add .
git commit -m "fix: correct revenue calculation double-counting returns"

# 4. Create PR, get emergency review/approval
# 5. Merge to main, deploy immediately
git checkout main
git merge hotfix/fix-revenue-bug
git push origin main

# 6. Also merge to develop so the fix isn't lost
git checkout develop
git merge main
git push origin develop

# 7. Tag the release
git tag -a v1.2.1 -m "Hotfix: revenue calculation bug"
git push origin v1.2.1
```

### Q37: You want to ensure no one commits broken SQL to the main branch. How do you enforce this?
**A:**
1. **Branch protection rules (GitHub/GitLab):**
   - Require PR reviews before merging
   - Require status checks to pass
   - Require up-to-date branches
   - Restrict pushes to main

2. **CI/CD pipeline (GitHub Actions):**
```yaml
# .github/workflows/sql-checks.yml
name: SQL Checks
on: [pull_request]
jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Install sqlfluff
        run: pip install sqlfluff
      - name: Lint SQL
        run: sqlfluff lint models/
      - name: Run dbt tests
        run: |
          pip install dbt-core dbt-postgres
          dbt deps
          dbt test
```

3. **Pre-commit hooks:**
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/sqlfluff/sqlfluff
    hooks:
      - id: sqlfluff-lint
```

### Q38: You need to share a specific analysis with a stakeholder who doesn't use Git. What's the best approach?
**A:**
1. **Export the notebook:** `File → Export → HTML` or use `jupyter nbconvert --to html`
2. **GitHub Gist:** Share a specific file or snippet via gist.github.com
3. **GitHub Pages:** Publish analysis as a static website from the repo
4. **ReviewNB:** Share a readable, commentable version of the notebook
5. **GitHub Releases:** Package analysis + data + code as a downloadable release
6. **Best practice:** For recurring reports, set up automated generation and email/Slack delivery from CI/CD

### Q39: Your repo has grown to 2GB because of committed data files. How do you clean it up?
**A:**
1. **Add to .gitignore:** Prevent future commits
2. **Remove from history:**
   ```bash
   git filter-repo --strip-blobs-bigger-than 10M
   # or for specific files:
   git filter-repo --path data/ --invert-paths
   ```
3. **Force push cleaned history:** `git push origin --force --all`
4. **Notify team:** Everyone must re-clone the repo
5. **Use Git LFS going forward:** `git lfs track "*.csv"`
6. **Alternative:** If filter-repo isn't available, use BFG Repo-Cleaner (faster for large repos)

### Q40: You're onboarding a new data analyst to your Git workflow. What do you teach them first?
**A:**
1. **Setup:** Install Git, configure name/email, generate SSH key, clone the repo
2. **Basic workflow:** `git pull` → make changes → `git add` → `git commit` → `git push`
3. **Branching:** Create feature branch, make PR, get review, merge
4. **.gitignore:** What not to commit (data, credentials, environments)
5. **Commit messages:** Why good messages matter, conventional commits format
6. **Conflict resolution:** What conflicts look like and how to fix them
7. **Review process:** How to review others' code and respond to feedback
8. **Hands-on:** Pair program on a real task, let them drive

### Q41: A team member force-pushed to main and overwrote commits. How do you recover?
**A:**
1. **Don't panic:** Git keeps unreachable commits for ~30 days (reflog)
2. **Find the lost commits:**
   ```bash
   git reflog  # Shows all reference updates
   git log --walk-reflogs  # More detailed
   ```
3. **Identify the commit hash before the force push**
4. **Restore:**
   ```bash
   git checkout main
   git reset --hard <good-commit-hash>
   git push --force-with-lease origin main
   ```
5. **If someone else has the good commits locally:**
   ```bash
   git fetch origin
   git branch recovery-branch <good-commit-hash>
   git push origin recovery-branch
   ```
6. **Prevention:** Enable branch protection rules that block force pushes to main
