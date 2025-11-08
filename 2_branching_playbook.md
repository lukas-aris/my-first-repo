# Git Branching Playbook — Create, Merge, and Rebase with Confidence

> This follow-up tutorial assumes you completed the **First Steps** guide (repo cloned locally, `main` branch clean). Work through each numbered step in order to learn branching, merging, resolving conflicts, and rebasing—complete with the exact commands to run and the outcome to expect.

---

## Before you start: quick repo setup (if you skipped the first tutorial)

Run these once so you have the same baseline as the “First Steps” guide.

1. Create the repo on GitHub (skip if it already exists):
   - Go to your GitHub profile → **Repositories** → **New**.
   - Give it a name (e.g., `branching-practice`), set it to **Public** (or Private), and check **Add a README** so `main` starts with one commit.
   - Leave `.gitignore` and license empty for now, then click **Create repository**.
2. Clone your GitHub repo.
3. (Optional) Create the practice file used in this tutorial so the steps match exactly:
   ```bash
   echo "# Notes" > notes.md
   git add notes.md
   git commit -m "Add notes placeholder"
   git push -u origin main
   ```

With those basics in place, you can jump straight into the branching scenarios below.

---

## Step 0: Start from a clean, up-to-date `main`

1. Open your terminal in the repo folder (e.g., `my-first-repo`).
2. Confirm you are on `main` and there are no pending changes:
   ```bash
   git status
   ```
   You should see `On branch main` and `nothing to commit`.
3. Make sure you have the newest commits from GitHub:
   ```bash
   git pull
   ```
   If everything is already up to date, Git prints “Already up to date”.

---

## Step 1: Create a feature branch and make a commit

1. Create the feature branch, then switch to it:
   ```bash
   git branch feature/add-summary
   git switch feature/add-summary
   ```
   Running `git branch` afterward shows the new branch with a `*` next to it.
2. Add a short project summary to `notes.md` (append a new section):
   ```bash
   cat <<'EOF' >> notes.md

   ## Summary
   - Practicing branch workflows.
   - Documenting each Git command we run.
   EOF
   ```
   (Feel free to add the same text manually in VS Code or any editor if you don’t want to run `cat`.)
   Run `tail -n 10 notes.md` to verify the new lines.
3. Commit the change using the add/commit routine you practiced earlier so the new summary lives on this branch.

---

## Step 2: Publish the branch so others can see it

1. Try a plain push first so you see what happens without an upstream branch:
   ```bash
   git push
   ```
   Git responds with an error similar to:
   ```
   fatal: The current branch feature/add-summary has no upstream branch.
   To push the current branch and set the remote as upstream, use

       git push --set-upstream origin feature/add-summary
   ```
2. Now push again while setting the upstream explicitly:
   ```bash
   git push -u origin feature/add-summary
   ```
   Or as stated with `git push --set-upstream origin feature/add-summary`.
   Future `git push` on this branch can omit the remote/branch names.
3. Confirm the branch exists remotely:
   ```bash
   git branch -vv
   ```
   You should see `feature/add-summary` tracking `origin/feature/add-summary`.
4. Open the repository in your browser on GitHub to double-check that the branch and its commit show up online.

---

## Step 3: Merge the branch back into `main` (fast-forward)

1. Return to the `main` branch:
   ```bash
   git switch main
   git pull
   ```
   Pull first to make sure your local `main` matches the remote.
2. Merge in the feature branch:
   ```bash
   git merge feature/add-summary
   ```
   Because `main` hasn’t diverged, Git fast-forwards and simply moves the branch pointer.
3. Push the updated `main` to GitHub:
   ```bash
   git push
   ```
4. Delete the merged branch locally (and optionally remotely):
   ```bash
   git branch -d feature/add-summary
   git push origin --delete feature/add-summary   # optional cleanup
   ```
   Skip the remote delete if you prefer to keep history online.

---

## Step 4: Practice resolving a merge conflict

In this section you’ll intentionally create conflicting changes to understand the workflow.

1. Start a new branch for a heading change, this time we create and switch to the new branch with one command:
   ```bash
   git switch -c feature/rename-heading
   ```
2. Edit the top heading in `notes.md` (line 1) to read `# Project Notes`. Use VS Code or another editor:
   ```bash
   code notes.md   # change the first line to: # Project Notes, save
   ```
3. Commit the updated heading with your usual workflow.
4. Switch back to `main` and make a different change to the same line to trigger a conflict.
   ```bash
   git switch main
   code notes.md   # change the first line to: # Notes (main branch edit), save
   ```
   Commit the main-branch edit with your normal workflow.
5. Attempt to merge the feature branch:
   ```bash
   git merge feature/rename-heading
   ```
   Git reports a conflict in `notes.md`.
6. Inspect conflict markers:
   ```bash
   git status
   code notes.md   # look for the <<<<<<<, =======, >>>>>>> markers
   ```
   Decide on the final text (e.g., combine both ideas into `# Project Notes (final)`) and remove the conflict markers. Save the file.
7. Finish the merge by staging the resolved file and completing the merge commit (Git will provide the default message).
   `git log --oneline -3` now shows the merge commit on top.
8. (Optional) Push the updated `main` to GitHub:
   ```bash
   git push
   ```
9. Clean up the now-merged branch:
   ```bash
   git branch -d feature/rename-heading
   ```

> **Key takeaway:** Resolving conflicts means editing the affected file to a final, conflict-free state, staging it, and completing the merge commit.

---

## Step 5: Rebase a branch onto an updated `main`

Rebasing rewrites your branch so it appears as if it was created from the latest `main`.

1. Ensure `main` has the latest commits:
   ```bash
   git switch main
   git pull
   ```
2. Create a new branch for copy edits:
   ```bash
   git switch -c feature/reword-summary
   ```
3. Update the summary bullet text in `notes.md` (for example, change “Documenting each Git command we run.” to “Documenting every Git command we practice.”), then commit the edit.
4. Simulate a teammate editing the *same* summary line on `main` so the rebase will hit a conflict:
   ```bash
   git switch main
   code notes.md   # change the summary bullet to: Documenting every Git exercise carefully.
   ```
   (Edit the file manually with VS Code or any editor if you prefer.) Save and commit this alternate wording on `main`.
5. Switch back to your feature branch, which is now behind `main`:
   ```bash
   git switch feature/reword-summary
   git status
   ```
6. Rebase onto the latest `main`:
   ```bash
   git rebase main
   ```
   Git halts with a conflict in `notes.md` because both commits modified the same line.
7. Resolve the conflict:
   ```bash
   code notes.md   # remove the <<<<<<<, =======, >>>>>>> markers and keep the final wording you want
   git add notes.md
   git rebase --continue
   ```
   (Use any editor you like—just ensure the conflict markers are gone before staging.)
8. Inspect the history graph:
   ```bash
   git log --oneline --graph --decorate --all | head -n 8
   ```
   You should see your branch commit directly on top of the newest `main` commit.
9. Fast-forward `main` to include the rebased work:
   ```bash
   git switch main
   git merge feature/reword-summary
   git push
   ```
10. Delete the branch:
   ```bash
   git branch -d feature/reword-summary
   ```

> **Need to update a remote branch after rebasing?** Because rebasing rewrites history, run:
> ```bash
> git push --force-with-lease origin feature/reword-summary
> ```
> This replaces the remote branch with your rebased commits but only if no one else has pushed in the meantime, which keeps teammates safe.

> **Safety tip:** Only rebase branches you own (or branches that no one else has pulled). Rewriting shared history can disrupt teammates.

---

## Step 6: Stash work in progress and move it to another branch

Need to jump to another task without committing half-done work? Stash the changes, switch branches, and reapply them exactly where you need them.

1. While on your current branch (say `feature/new-intro`), make edits so `git status` shows modified files.
2. Park the changes in a stash:
   ```bash
   git stash push -m "WIP: new intro section"
   ```
   Your working tree is now clean. Run `git stash list` to see numbered entries (`stash@{0}`, `stash@{1}`, …).
3. Switch to the branch that needs quick attention (or create it):
   ```bash
   git switch main
   git pull
   git switch -c hotfix/fix-typo
   ```
4. Replay the stashed work onto this branch:
   ```bash
   git stash pop
   ```
   `pop` reapplies the diff and removes that stash entry. To keep it around (maybe to test on multiple branches), use `git stash apply stash@{0}` instead and drop it manually later with `git stash drop stash@{0}`.
5. Continue editing or commit the resurrected work as usual. If conflicts appear, resolve them just like a merge conflict, then stage and continue.

> **Extras:** Include untracked files with `git stash push -u`. Peek inside a stash via `git stash show -p stash@{2}`. You can create multiple stashes and apply any one of them wherever it makes sense.

---

## Step 7: Quick recap (cheat sheet)

```bash
# Create & publish a feature branch
git branch feature/add-summary
git switch feature/add-summary
# ...edit files...
# (commit your changes using your normal add/commit flow)
git push -u origin feature/add-summary

# Fast-forward merge back into main
git switch main
git pull
git merge --ff-only feature/add-summary
git push
git branch -d feature/add-summary

# Handle a merge conflict
git switch -c feature/rename-heading
# ...edit heading...
# (commit the heading change)
git switch main
# ...make conflicting edit...
# (commit the main-branch change)
git merge feature/rename-heading
# resolve conflict markers, then:
# (stage resolved files and complete the merge commit)

# Rebase a branch onto latest main
git switch -c feature/reword-summary
# ...update summary text...
# (commit the summary change)
git switch main
# ...teammate commit...
git switch feature/reword-summary
git rebase main
git push --force-with-lease origin feature/reword-summary   # update the remote branch safely
git switch main
git merge --ff-only feature/reword-summary
git push

# Stash unfinished work and move it elsewhere
git stash push -m "WIP intro refresh"
git switch main
git pull
git switch -c hotfix/fix-typo
git stash pop   # or: git stash apply stash@{0}
```

---

## You’re set!
You just practiced the full lifecycle: creating branches, sharing them, merging (both fast-forward and with conflicts), and rebasing to keep history clean. Keep experimenting—try `git reflog` or `git branch -a` next to deepen your understanding.
