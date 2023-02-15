## Rewriting History
#git 

### Changing the Last Commit
- `git commit --amend`: 
	- Changing message: Loads previous commit message to an editor, where changes can be made and saved. A new commit is added which becomes the latest commit.
	- Changing commit content: Make the changes, stage them, and run this command. It will *replace* the last commit with the new commit
 
### Changing Multiple Commit Messages
- `git rebase --interactive`: Enables reordering, squashing, splitting, deleting, and much more of multiple commits in an interactive manner. Eg.: `git rebase -i HEAD~3` opens an interactive session in a text editor with the options.

### The Nuclear Option: git-filter-repo
- `git-filter-repo`: Enables rewriting of a large number of commits in a scriptable way. Use cases include removing a file from every commit, changing source directory, or replacing email address in all commits. Should be used with caution since it rewrites the SHA-1 for most commits and may cause issues for developers who have based work on published commits. It must also be noted that `git-filter-repo` is a Python script which replaces `git filter-branch`, the original command, since the latter has many pitfalls.