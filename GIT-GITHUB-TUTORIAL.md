# Git & GitHub Step-by-Step Tutorial

> **Goal:** Create this RH294 lab repository locally with Git, then publish it to GitHub.
> This tutorial covers everything from installation to pushing your first commit.

---

## Part 1 — Install and Configure Git

### Step 1.1 — Install Git

```bash
# RHEL / AlmaLinux / CentOS
sudo dnf install git -y

# Verify
git --version
```

### Step 1.2 — Set your identity

Git needs to know who you are for commit history. Run these once on your system:

```bash
git config --global user.name "Your Full Name"
git config --global user.email "you@example.com"
```

### Step 1.3 — Set default branch name to `main`

```bash
git config --global init.defaultBranch main
```

### Step 1.4 — Set a comfortable editor (optional)

```bash
git config --global core.editor vim   # or nano, code, etc.
```

### Step 1.5 — Verify your configuration

```bash
git config --list
```

---

## Part 2 — Initialize the Local Repository

### Step 2.1 — Create the project directory

```bash
mkdir ~/rh294-lab
cd ~/rh294-lab
```

### Step 2.2 — Initialize Git

```bash
git init
```

Expected output:

```
Initialized empty Git repository in /home/youruser/rh294-lab/.git/
```

### Step 2.3 — Create a .gitignore file

This tells Git to ignore files that shouldn't be tracked (Ansible vault passwords, Vagrant machine state, etc.):

```bash
cat > .gitignore << 'EOF'
# Vagrant
.vagrant/

# Ansible
*.retry
.vault_pass
*.vault_pass
vault_password_file

# Editor and OS
*.swp
*~
.DS_Store
__pycache__/
*.pyc
EOF
```

---

## Part 3 — Add Your Files

### Step 3.1 — Check repository status

```bash
git status
```

You'll see all new files listed as **Untracked files** in red.

### Step 3.2 — Stage all files

```bash
git add .
```

This stages every file in the current directory recursively (respecting `.gitignore`).

### Step 3.3 — Verify what is staged

```bash
git status
```

Files now appear in green under **Changes to be committed**.

### Step 3.4 — Make your first commit

```bash
git commit -m "Initial commit: RH294 lab structure, Vagrantfile, questions and solutions"
```

A commit message should be:
- Written in the **imperative mood** ("Add", "Fix", "Update" not "Added", "Fixed")
- Under 72 characters for the subject line
- Descriptive enough to understand the change without reading the diff

### Step 3.5 — View commit history

```bash
git log --oneline
```

---

## Part 4 — Create the GitHub Repository

### Step 4.1 — Create a GitHub account (if needed)

Go to https://github.com and sign up.

### Step 4.2 — Create a new repository on GitHub

1. Click the **+** icon → **New repository**
2. Fill in:
   - **Repository name:** `rh294-lab`
   - **Description:** `RH294 Ansible Automation lab — EX294 exam prep`
   - **Visibility:** Public or Private (your choice)
3. **Do NOT** initialize with a README, .gitignore, or license  
   (you already have these locally)
4. Click **Create repository**

GitHub shows a page with commands — you'll use the **"push an existing repository"** section.

---

## Part 5 — Connect Local Git to GitHub

### Step 5.1 — Set up SSH authentication (recommended)

#### Generate an SSH key (if you don't have one):

```bash
ssh-keygen -t ed25519 -C "you@example.com"
# Accept default path (~/.ssh/id_ed25519)
# Set a passphrase (recommended) or press Enter for none
```

#### Copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

#### Add the key to GitHub:

1. Go to **GitHub → Settings → SSH and GPG keys**
2. Click **New SSH key**
3. Paste the output of the command above
4. Click **Add SSH key**

#### Test the connection:

```bash
ssh -T git@github.com
```

Expected output:

```
Hi yourusername! You've successfully authenticated, but GitHub does not provide shell access.
```

---

### Step 5.2 — Add GitHub as the remote origin

```bash
# Replace <your-username> with your actual GitHub username
git remote add origin git@github.com:<your-username>/rh294-lab.git
```

Verify the remote was added:

```bash
git remote -v
```

Expected output:

```
origin  git@github.com:<your-username>/rh294-lab.git (fetch)
origin  git@github.com:<your-username>/rh294-lab.git (push)
```

---

## Part 6 — Push to GitHub

### Step 6.1 — Push the main branch

```bash
git push -u origin main
```

The `-u` flag sets `origin/main` as the upstream tracking branch. After this, you can use just `git push` for future pushes.

### Step 6.2 — Verify on GitHub

1. Go to `https://github.com/<your-username>/rh294-lab`
2. You should see all your files, including the rendered `README.md`

---

## Part 7 — Ongoing Git Workflow

After the initial setup, use this workflow for every change:

```bash
# 1. Check what changed
git status

# 2. Review the actual diff (optional but good habit)
git diff

# 3. Stage changes
git add <file>          # specific file
git add .               # all changed files

# 4. Commit
git commit -m "Add solution for section 05 task control"

# 5. Push to GitHub
git push
```

---

## Part 8 — Branch Workflow (Recommended)

Use branches to isolate work on each exam section:

```bash
# Create and switch to a new branch
git checkout -b section-05-task-control

# ... do your work, add files, commit ...

git add solutions/05-task-control/solutions.md
git commit -m "Add task control solutions"

# Push the branch to GitHub
git push -u origin section-05-task-control

# When done, merge back to main
git checkout main
git merge section-05-task-control

# Delete the branch after merging
git branch -d section-05-task-control
```

---

## Part 9 — Essential Git Commands Reference

| Command | What it does |
|---------|-------------|
| `git init` | Initialize a new repo |
| `git clone <url>` | Copy a remote repo locally |
| `git status` | Show working tree status |
| `git add <file>` | Stage a file |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Commit staged changes |
| `git push` | Push commits to remote |
| `git pull` | Fetch + merge remote changes |
| `git log --oneline` | Compact commit history |
| `git diff` | Show unstaged changes |
| `git diff --staged` | Show staged changes |
| `git branch` | List branches |
| `git checkout -b <name>` | Create + switch to new branch |
| `git merge <branch>` | Merge branch into current |
| `git stash` | Temporarily shelve changes |
| `git stash pop` | Restore stashed changes |

---

## Part 10 — Troubleshooting

| Problem | Solution |
|---------|---------|
| `git push` rejected (non-fast-forward) | Run `git pull --rebase` first, then push |
| Committed a file that should be ignored | `git rm --cached <file>`, add to `.gitignore`, recommit |
| Wrong commit message | `git commit --amend -m "new message"` (before pushing) |
| Accidentally committed vault password | `git reset HEAD~1`, add to `.gitignore`, commit again. If already pushed, rotate the secret immediately |
| `Permission denied (publickey)` | Verify SSH key is added to GitHub; run `ssh -T git@github.com` |
| Need to undo last commit (keep changes) | `git reset --soft HEAD~1` |
| Need to undo last commit (discard changes) | `git reset --hard HEAD~1` (destructive!) |
