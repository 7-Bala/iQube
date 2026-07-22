# iQube

Study notes from my Obsidian vault, written in Markdown.

## Contents

### Day 1

| Note | Covers |
| --- | --- |
| Components of a Computer | PSU, GPU, motherboard, CPU, RAM, storage, network adapter |
| Linux | Philosophy, core components (bootloader, kernel, daemons), file hierarchy |
| OverTheWire Bandit | Level-by-level notes and commands, L0 through L14 |

---

## Pushing changes

Notes are edited in Obsidian. Git has no idea anything changed until you tell
it, so after a writing session you run three commands: **add**, **commit**, **push**.

### 1. Open the folder in Terminal

```bash
cd "/Users/bala/Documents/Obsidian Vault/Users/bala/Documents/iQube"
```

The quotes matter — the path contains spaces, and without them the shell reads
`/Users/bala/Documents/Obsidian` as the whole argument and fails.

### 2. Check what changed

```bash
git status
```

Modified files show as `modified:`, brand-new ones as `untracked`. Worth doing
every time — it's how you catch a file you didn't mean to include.

### 3. Stage, commit, push

```bash
git add -A
git commit -m "Day 2: file permissions and chmod"
git push
```

What each step actually does:

- **`git add -A`** — stages every change in the folder: edits, new files, and
  deletions. Staging is a holding area; nothing is recorded yet.
- **`git commit -m "..."`** — records the staged snapshot in local history with
  a message. This is saved on your Mac only. GitHub still knows nothing.
- **`git push`** — uploads any local commits to GitHub. This is the step that
  makes the change visible online.

Commit messages should say what changed, not that something changed.
`"Add RAM section to Components"` beats `"update"` when you're reading
history six months from now.

### The one-liner

Once the three steps feel routine:

```bash
git add -A && git commit -m "your message" && git push
```

The `&&` means each command only runs if the previous one succeeded, so a
failed commit won't trigger a push.

---

## If you edit a note on GitHub directly

GitHub now has a commit your Mac doesn't, and `git push` will be rejected to
stop the two histories from diverging. Pull first:

```bash
git pull --rebase
git push
```

`--rebase` replays your local commits on top of the remote ones, which keeps
history a straight line instead of adding a merge commit.

---

## Handy commands

| Command | What it tells you |
| --- | --- |
| `git status` | What's changed since the last commit |
| `git diff` | The exact lines you changed, before staging |
| `git log --oneline -5` | The last five commits |
| `git log --oneline origin/main..main` | Commits sitting locally, not yet pushed |
| `git restore <file>` | Throw away uncommitted changes to a file |

---

## Syncing from inside Obsidian

The [Obsidian Git](https://github.com/Vinzent03/obsidian-git) plugin is installed and
handles commit, pull, and push without leaving the app.

Because this repo sits *below* the vault root, the plugin needs
**Settings -> Git -> Advanced -> Custom base path** set to:

```
Users/bala/Documents/iQube
```

Without it the plugin reports "Can't find a valid git repository", since the vault
root itself has no `.git`.

Current configuration:

| Setting | Value |
| --- | --- |
| Auto commit-and-sync interval | 5 minutes |
| Auto commit-and-sync after stopping file edits | On |
| Auto pull interval | 10 minutes |

It only runs while Obsidian is open. When it's closed, use the manual steps above.

---

## Repo setup

Remote is `origin` → `https://github.com/7-Bala/iQube.git`, branch `main`.
Authentication goes through the GitHub CLI (`gh auth login`), so pushes don't
prompt for a password. Check it any time with `gh auth status`.
