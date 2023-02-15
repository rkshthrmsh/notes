## Stashing and Cleaning
#git 

### Stashing
Stashing is a technique to store the (dirty) state of a working directory, which includes modified tracked files and staged changes, and saves it into a "stack" which can be applied later. Stashed changes can be applied on the branch they were created on or any other branch. 
- `git stash`: Stash changes in the working branch
	- `git stash --keep-index`: Stash all content and also leave staged content in the current branch
	- `git stash --include-untracked` or `-u`: Stash all untracked files
	- `git stash --all` or `-a`: Include all files, even ignored ones
	- `git stash --patch`: Interactive stashing of patches
- `git stash list`: View all stashed changes
- `git stash apply`: Apply stash on current branch
	- `git stash apply --index`: Apply stash with staged changes included
- `git stash drop`: Apply stash and drop it from stash list
- `git stash branch <new branchname>`: Create a new branch and apply the changes that are about to be stashed on to the new branch

 ### Cleaning
 Cleaning will remove untracked files from the working directory. By default ignored files will not be cleaned.
 - `git clean`: Remove untracked files from working directory. Files will not be recoverable, so be careful.
	 - `git clean -f`: Since it is a premanent removal, `.config` requires the force flag by default.
	 - `git clean -d`: Removes empty directories which may have resulted from the removal of untracked files
	 - `git clean --dry-run` or `-n`: Shows a list of files that would be removed in case the working directory is cleaned
	 - `git clean -x`: Also remove ignored files
	 - `git clean -i`: Interactive clean session