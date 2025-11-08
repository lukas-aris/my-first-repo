# Git First Steps — From New Repo to Push/Pull

> Follow these steps in order to go from “nothing on GitHub” to a synced local project. Each step states **what to click**, **which command to run**, and **what you should see** before moving on.

---

## Step 0: Confirm prerequisites (run these before you start)

1. Check Git is installed:
   ```bash
   git --version
   ```
   You should see a version number.
2. Test that your SSH key works with GitHub:
   ```bash
   ssh -T git@github.com
   ```
   Expect a “Hi <username>!” greeting. If you get a warning about authenticity, type `yes`.
3. Open VS Code once to make sure it launches (`code` command should already work). If `code` is not found, enable the Shell Command from VS Code (`Cmd+Shift+P` → “Shell Command: Install 'code' command in PATH”).

---

## Step 1: Create the repository on GitHub

1. In a browser, go to GitHub and click your profile icon → **Repositories** → **New**.
2. Configure the form exactly as follows:
   1. Set **Repository name** to `my-first-repo` (or any memorable name).
   2. Enter **Description** as “First steps into the magic world of git”.
   3. Select **Visibility** → **Public** so others can see it.
   4. Check ✅ **Add a README file** to create an initial commit.
   5. Leave **.gitignore** unchecked; you will add one manually in **Step 4**.
   6. Leave **Choose a license** blank (licensing is outside this tutorial).
   7. Keep **Template** set to **No template** so the repo starts plain.
3. Click **Create repository**. You should land on the new repo page and see the README content.


> **Need a refresher?** A `README.md` is the landing page for your project. It explains what the project does and how to use it. A `.gitignore` tells Git which files to skip (e.g., `dist/`, `*.log`). You will add one in **Step 4** and share it with every collaborator.

---

## Step 2: Clone the repo via SSH

1. On the repo page you just created, click the green **Code** button, choose the **SSH** tab, and copy the SSH URL (it looks like `git@github.com:YOUR-USER/my-first-repo.git`).
2. In your terminal, run:
   ```bash
   git clone git@github.com:YOUR-USER/my-first-repo.git
   cd my-first-repo
   ```
   Replace `YOUR-USER` and the repo name with your actual values.
3. Confirm you are inside the new folder:
   ```bash
   pwd
   ```
   The path should end with your repository name.
4. Configure the identity Git will record in your commits (run once per machine):
   ```bash
   git config --global user.name  "Your Name"
   git config --global user.email "you@example.com"
   ```
5. Double-check the settings took effect:
   ```bash
   git config --list --show-origin
   ```
   Look for the `user.name` and `user.email` entries. If you prefer repo-specific settings, rerun the config commands without `--global` while inside the repo.

---

## Step 3: Make your first commit from the terminal

1. Create a file and add content:
   ```bash
   echo "# Notes" > notes.md
   echo "First line of my notes." >> notes.md
   ```
   Verify the contents by opening the file in VS Code or, if your shell supports it, running `cat notes.md` (use `type notes.md` on Windows Command Prompt).
2. Check what Git sees:
   ```bash
   git status
   ```
   You should see `notes.md` listed under “Untracked files”.
3. Stage the file so it will be part of the next commit:
   ```bash
   git add notes.md
   git status
   ```
   Now `notes.md` appears under “Changes to be committed”.
4. (Optional) If you stage the wrong thing, undo it and re-stage:
   ```bash
   git restore --staged notes.md
   git status
   git add notes.md
   ```
5. Commit the staged change with a clear message:
   ```bash
   git commit -m "Add notes.md with first line"
   ```
   Git should report `1 file changed` and create a commit ID.

> **Commit message tip:** Keep it short and action-oriented. Examples: `Add user login handler`, `Fix null parser check`, `Update README with setup steps`.

---

## Step 4: Add a `.gitignore` and see it work

1. Create the `.gitignore` file **in the root of your repository** (same folder as `.git/`, `notes.md`, etc.). Use your editor (e.g., VS Code) or run:
   ```bash
   printf "# Logs\nlogs/\n\n# Temporary files\ntmp/\n" > .gitignore
   ```
   Feel free to customize these patterns later to match your project.
2. Simulate files that you do **not** want in version control:
   ```bash
   mkdir -p logs
   echo "debug info" > logs/debug.log
   mkdir -p tmp
   echo "temp data" > tmp/cache.tmp
   ```
   These mimic log folders or temporary caches that appear during development.
3. Check what Git is tracking now:
   ```bash
   git status
   ```
   `.gitignore` should show under “Untracked files”, while `logs/` and `tmp/` stay hidden because they match your ignore rules.
4. Commit the ignore rules so the whole team shares them:
   ```bash
   git add .gitignore
   git status
   git commit -m "Add starter .gitignore"
   ```
   The ignored files remain on disk, but Git keeps ignoring them.

> Need to add more exceptions later? Edit `.gitignore`, run `git status` to confirm the effect, then commit the update.

---

## Step 5: Make and commit changes in VS Code

1. Launch VS Code from the repo folder:
   ```bash
   code .
   ```
   VS Code should open with the repo contents in the Explorer pane.
2. Edit `notes.md` in VS Code—add another line (e.g., “Second line of notes.”) and save the file (`Cmd+S`).
3. Preview the change in the terminal before staging:
   ```bash
   git diff notes.md
   ```
   The highlighted output shows exactly what lines will be added or removed.
4. Review the same diff inside VS Code: open the **Source Control** view (left sidebar, branch icon) and click `notes.md`. VS Code opens a side-by-side diff view that mirrors the terminal output.
5. Stage the change by clicking the `+` icon next to `notes.md` (or right-click → **Stage Changes**). The file moves to **Staged Changes**.
6. (Optional) Confirm what’s staged from the terminal:
   ```bash
   git diff --staged notes.md
   ```
   This highlights only the lines that will be included in the commit.
7. Enter a commit message in the input box (e.g., `Update notes with second line`) and click the checkmark icon (Commit). VS Code may ask to stage; choose **Always** if prompted.
8. Verify in the TERMINAL (inside VS Code or your shell) that the commit exists:
   ```bash
   git log --oneline
   ```
   You should see the VS Code commit at the top and your earlier terminal commit below it.

---

## Step 6: Push and pull to stay in sync with GitHub

1. Confirm you are on the branch you expect:
   ```bash
   git status
   ```
   It should say `On branch main` and `nothing to commit`.
2. Push the branch to GitHub the first time. The `-u origin main` flag tells Git to remember that `main` should sync with `origin/main`, so later you can just run `git push` without extra arguments:
   ```bash
   git push -u origin main
   ```
3. When you or a teammate push new commits to GitHub, bring them down with `git pull`. This command fetches fresh commits from `origin/main` and merges them into your local `main`, keeping you up to date:
   ```bash
   git pull
   ```
4. Push routine updates with `git push`. Because of step 2, Git already knows which remote branch to update:
   ```bash
   git push
   ```
5. Anytime you wonder “what’s going on?”, run the quick inspection trio:
   ```bash
   git status
   git remote -v
   git log --oneline --graph --decorate --all
   ```
   `git status` lists staged/unstaged changes, `git remote -v` shows the URLs of your remotes, and `git log --oneline --graph --decorate --all` prints a concise commit history graph so you can visualize branches.

### Handling common hiccups

**“Nothing to commit, working tree clean”**  
You haven’t changed any tracked files. Edit or create files first.

**Push rejected (remote has new commits)**  
Someone else pushed first. Pull, resolve conflicts, then push:
```bash
git pull --rebase
# fix any conflicts in your editor (explained in the next tutorial), then:
git add <fixed-file>
git rebase --continue
git push
```

**Accidentally staged too much**  
```bash
git restore --staged .
```
Then re-stage only what you want:
```bash
git add path/you/actually/want
```

---

## Step 7: Quick recap (optional cheat sheet)

```bash
# Step 2: Clone and enter the repo
git clone git@github.com:YOUR-USER/my-first-repo.git
cd my-first-repo

# Step 2 (once per machine): Configure identity
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"

# Step 3: Create notes.md and commit via CLI
echo "# Notes" > notes.md
echo "First line of my notes." >> notes.md
git status
git add notes.md
git commit -m "Add notes.md with first line"

# Step 4: Add a .gitignore and commit it
printf "# Logs\nlogs/\n\n# Temporary files\ntmp/\n" > .gitignore
mkdir -p logs
echo "debug info" > logs/debug.log
mkdir -p tmp
echo "temp data" > tmp/cache.tmp
git add .gitignore
git commit -m "Add starter .gitignore"

# Step 5: Edit and commit from VS Code
code .
# (Use the Source Control panel to stage and commit GUI-side.)

# Step 6: Sync with GitHub
git push -u origin main   # first time only
git pull                  # grab remote updates
git push                  # share your new commits
```

---

## That’s it!
You created a repo, cloned via SSH, committed via CLI, set up a `.gitignore`, edited via VS Code, and learned push/pull.  
