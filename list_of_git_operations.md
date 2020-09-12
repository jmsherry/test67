# List of git commands

## Creating a git repository

1. `git init`
   1. You'll see a `.git` folder appear
   2. Delete it to 'un-git'
   3. If you're using someone elses repo as a starting point, then `git clone` (see 'remotes' later)

## The stages of git

1. 'Untracked': File[s] have appeared in the directory and git is aware they are there but is not tracking their changes at this point
2. 'Working Tree': Files and changes to them are tracked
3. 'Staging'/'Index': When you want to group files to 'commit' to complete a work task
4. 'Committed': When you push that group of files into your local repository. You are putting these changes 'on the record'. It is only once you've done this that you can then send them anywhere/share them.

### How to see what's at those stages

| COMMAND | Untracked | Working Tree | Staged | Committed |
| ------- | --------- | ------------ | ------ | --------- |
| status  | ✅        | ✅           | ✅     |           |
| diff    |           | ✅           | ✅     | ✅        |
| log     |           |              |        | ✅        |

_status_: show's where git considers the file to be
_diff_: Shows the difference between what's in your local repo and what you have now
_log_: Shows the history of the repo as you commit things

### Moving between stages

#### Untracked -> deleted

1. `git clean` removes untracked files from the working copy

#### Untracked <-> working copy

1. To add them to the working tree do `git add -N <file>` (any other `add` will put them on staging)
2. To move back to 'untracked' `git rm --cached <file>` (`git rm -r --cached <directory>`)

#### working copy/untracked <-> staging

1. To add a file to staging `git add <file>`
2. If you 'unstage' a staged file it returns to the 'working tree'
3. To unstage `git restore <file>` (or `git reset [<file>]`)

#### staging <-> committed

1. To commit the staged files to the repository as one: `git commit` (`git commit -m "<message>"`)
   1. To change the commit message of the last commit: `git commit --amend -m "<new message>"`
   2. To add files you forgot (and keep the same message), stage them, then: `git commit --amend --no-edit`
   3. Anything more complex than that and you're in rebase territory!
2. To undo the last commit:
   1. Back to staging: `git reset --soft HEAD~1` (can't do filepaths)
   2. Back to working tree: `git reset --mixed HEAD <file>` (`--mixed` is default and not required)
   3. Remove totally: `git reset --hard HEAD~2` (can't do filepaths)
3. This moves back to the last recorded commit. If you want to go back to a previous commit do `HEAD~<n>` (e.g. `HEAD~2`)

#### Setting local files back to the last commit

1. If you want to discard your work [on certain files]: `git checkout HEAD [files]`

#### Undoing a particular commit (particularly if it has left your local system)

1. `git revert <sha>` - creates a new commit at the HEAD of the branch with the changes made in that commit removed. You have to agree and commit it yourself.

---

## Branches

### What is a branch

1. When you create a repository you get a default branch called `master`
2. All branches are just a line of sequential submits (with an ID, called a `SHA` or 'Secure Hash Algorithm')
3. To see them you can run `git log` (`git log --oneline` is faster, and shows the short aliases of the SHAs (the first ~7 digits))
4. Each submit is just a list of differences (run `git log --stat` to see them)
5. Sometimes, particularly if you're trying something new, or if you need to maintain different versions of the code (for hosting/environment reasons), then you may create a new branch

### Seeing branches

1. `git branch` will show you what branches you have locally
   1. Flags: `-r` to see remote branches; `-a` to see all

### Checking out

1. `git checkout` was an accident of design and can let you switch branches or go to a particular commit on your current branch. It was supposed to let you move to any point in history in the repo BUT its dual role became confusing...`git switch` now handles branches, whilst `git restore` handles changes to the files
2. To move to a branch `git checkout <name>` (You cannot move [in one command] to a place on that branch - you start at the `HEAD`) (NEW: `git switch <name>` (`git switch -` if it's `master`))
3. To move to a commit `git checkout <SHA>`
   1. When you move here it creates you a temporary state, called a 'detached head state'.
   2. You can mess around with stuff here and decide if you want to keep what you've done
      1. To destory it, just checkout a branch or HEAD
      2. To keep it, branch from here and then commit the changes (you can then merge whenever you're ready)

### Creating branches

1. To create a branch (without moving to it): `git branch <name>`
2. To create a branch and check it out simultaneously: `git checkout -b <name>` (NEW: `git switch -c <name>`)

### Deleting branches

1. `git branch -d <name>` is the safe option that will alert you if you have outstanding changes before deleting. (`-D` will force the delete and not ask)

### Merging

1. When you've completed work you will want to move it from one branch to another. Simply move to the branch you want to receive the changes and do `git merge <branch with changes on>`
2. Git has `auto-merge`, which generally works quite well
3. If not then you will have to manually merge
4. To see JUST files with conflicts, use `git diff --name-only --diff-filter=U` (To save time, do `git config --global alias.conflicts "diff --name-only --diff-filter=U"` and then `git conflicts` whenever you want to see)
5. To see this activity in the logs `git log --all --decorate --oneline --graph`

### Stashing

1. Sometimes git will not let you change branch without saving changes (for fear of loss); sometimes you will realise you've made changes on the wrong branch. This is where you need `git stash`
2. Git stash stashes your changes in a separate place
3. You can then leave the branch OR go to the branch you _meant_ to apply the changes to and do: `git stash apply [stash number]` (or `git stash pop` if you want to remove the changes from the stash once applied)

---

## Remotes

1. Sooner or later you're going to have to work with other people (or 3rd parties; or remote resources)
2. IF they have set up a repository for you then you should `git clone <repo url> [<what the directory it downloads into should be called>]`
   1. Doing this sets up the relationship between your copy and the remote
3. IF you are going to create a remote of your own then you'll need to set that up yourself on GitHub/Lab/Whatever
4. To add a remote connection to your repo: `git remote add <name> <remote url>`
5. To delete one: `git remote remove <name>`
6. To send data: `git push <name> <branch>` (use `-u` the first time, to set up tracking)
7. To get data from a remote (only to your repo, not your working files): `git fetch` - this will allow you to see if you are ahead or behind with changes
8. To get data and merge it into your working directory `git pull <remote> <branch>` (just `git pull` pulls your current branch from 'origin')
9. Pulling remote branches down: `git checkout -b test <name of remote>/test` (or `git checkout -t <name of remote>/test`)
