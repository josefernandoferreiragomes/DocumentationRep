# Git Commands Quick Reference

## Basic Commands
- **`git status`**: Show the working directory status.
- **`git pull`**: Fetch and merge changes from a remote repository.
- **git add [file]**: Add a file to the staging area.
- **git add .** : Adds all modified files to the staging area.
- **git commit -m "[message]"**: Commit changes with a message.
- **`git push`**: Push changes to a remote repository.
- **git init**: Initialize a new Git repository.
- **git clone [url]**: Clone an existing repository.
- **git remote show origin**: Show remote origin

## Branching
- **`git branch`**: List branches.
- **git branch [branch-name]**: Create a new branch.
- **git checkout [branch-name]**: Switch to a branch.
- **git merge [branch-name]**: Merge a branch into the current branch.
- **git branch -d [branch-name]**: Delete a branch.

## Remote Repositories
- **`git remote -v`**: List remote repositories.
- **`git fetch [remote]`**: Fetch changes from a remote repository.
- **git pull [remote] [branch]**: Pull changes from a remote repository.
- **git push [remote] [branch]**: Push a branch to a remote repository.
-
- **git remote add [name] [url]**: Add a new remote repository.

## Undoing Changes
- **`git reset [file]`**: Unstage a file.
- **`git reset --hard`**: Reset the working directory.
- **`git reset --hard [commit]`**: Reset the working directory to a specific commit.
- **git checkout -- [file]**: Discard changes in a file.
- **git revert [commit]**: Revert a commit by creating a new commit.

## Viewing History
- **git log**: Show commit history.
- **git log --oneline**: Show commit history in a compact format.
- **git diff**: Show changes between commits, branches, or files.
- **git show [commit]**: Show changes in a specific commit.

## Stashing
- **`git stash`**: Stash changes in a dirty working directory.
- **`git stash drop`**: Drop a specific stash.
- **git stash apply**: Apply stashed changes.
- **git stash list**: List all stashes.
- **git stash pop**: Apply the most recent stash.
- **git stash push**: Temporarily save your changes without committing them.

## Rename, move
- **`git mv old-filename new-filename`**: Rename a file
- **`git mv filename path/to/new-directory/`**: Move a file to a different directory
- **`git mv old-filename path/to/new-directory/new-filename`**: Move and rename a file:
- **`git mv old-directory-name new-directory-name`**: Rename a directory
- **`git mv old-directory-name path/to/new-directory/`**: Move a directory
  
## Tags
- **git tag**: List tags.
- **git tag [tag-name]**: Create a new tag.
- **git push [remote] [tag-name]**: Push a tag to a remote repository.
- **git push --tags**: Push all tags to a remote repository.

## Configuration
- **git config --global user.name "[name]"**: Set the global username.
- **git config --global user.email "[email]"**: Set the global email.
- **git config --list**: List all configuration settings.

## Aliases
- **git config --global alias.[alias-name] "[command]"**: Create a new alias.
- **git config --global alias.co "checkout"**: Example: Create an alias for `checkout`.

## Miscellaneous
- **git help [command]**: Get help for a specific Git command.
- **git blame [file]**: Show what revision and author last modified each line of a file.
- **git clean -f**: Remove untracked files from the working directory.

## References:

Official page:
  https://git-scm.com/doc

Dev.to:
  https://dev.to/chintanonweb/git-like-a-pro-10-things-i-regret-not-knowing-earlier-24kp
