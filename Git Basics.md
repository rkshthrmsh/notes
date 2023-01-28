# [[Git]] Basics
#git 

## Getting a Git Repository
- `git init`: Create a new `.git` directory with all repository files.
- `git clone <url>`: Clone an entire repository to the local system to create the working tree.

## Recording Changes to the Repository
- `git status`: Check status of files in working tree.
- `git add`: Track new files / stage modified files.
- `git diff` (`git difftool` for graphical): Check changes that have not been staged. 
- `git diff --staged` or `git diff --cached` (`git difftool --staged` for graphical): Compare staged changes to last commit.
- `git commit`: Commit all staged files.
- `git commit --amend`: Replace previous commit to include minor changes.
- `git rm <file>`: Remove and stage the file's removal. File will be gone after next commit and no longer tracked.
- `git mv <file> <new_file>`: Convenience function for renaming files and staging the renamed file.

## View Commit History
- `git log`: View commit history. Has a wide range of options to narrow down author, date range, output formats, graph mode, etc.

## Undoing Things
- `git restore <file>`: Un-modify a modified file.
- `git restore --staged <file>`: Un-stage a staged file.

## Working with Remotes
- `git remote`: Show list of all remotes.
- `git remote add <name> <URL>`: Add a new remote repository from the URL to your working tree and give it a name.
- `git remote show <remote>`: Show additional information about remote repository.
- `git remote remove <remote>`: Remove remote
- `git remote rename <remote>`: Rename remote
- `git fetch <remote>`: Get all data from the remote project not already in working tree. Does not merge automatically.
- `git push <origin> <branch>`: Push any commits to the remote repository.
- `git pull`

## Tagging
- `git tag`: List existing tags
- `git tag -a <tagname>`: Create an annotated tag. Other option is to create a lightweight tag. Annotated tags are preferable since they contain additional information--checksum, tagger name, email, date, message, and can be signed with GNU Privacy Guard.
- `git push <remote> <tagname>` or `git push <remote> --tags`: To push tags to the remote. Tags are not pushed automatically.
- `git tag -d <tagname>`: Delete tag in local repository.
- `git push <remote> --delete <tagname>`: Delete tag from remote server
- `git checkout -b <branchname> <tagname>`: To checkout a tag, first create a branch and then checkout the tag.