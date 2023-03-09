# [[Git]] Branching
#git

A branch in Git is a lightweight movable pointer to any commit. Each commit object represents a snapshot of the local repository, and contains commit metadata and a pointer to the root tree. The tree object lists the contents of the directory and specifies file names an their equivalent `blobs`.

![[Pasted image 20230118192507.png|480x270]]

As mentioned earlier, each branch is a simple pointer to one of the commit objects. A special pointer called `HEAD` keeps a track of the current branch.

![[Pasted image 20230118192736.png|480x270]]



- `git branch <branchname>`: Creates new branch
- `git checkout <branchname>`: Moves HEAD to a particular branch
- `git checkout -b <branchname>`: Create a branch and checkout into the branch
- `git merge <branchname>`: Merge `branchname` branch into current branch.
	- Merge can be a simple `Fast-Forward` when commit object of `branchname` is a direct child of the current branch's commit object.
	  ![[Pasted image 20230118205626.png|480x270]]
	- Or it can be a merge commit, wherein three snapshots are used to create a new, special commit object which has two parents. 'C6' in the image below.
	  ![[Pasted image 20230118205652.png|480x270]]

- `git branch -d <branchname>`: Delete branch
- `git mergetool`: Visual merge tool
- `git branch --move <branchname> <new_branchname>`: Rename a branch in the local repository
  `git push --set-upstream <remote> <new_branchname>`: Rename the branch name in the remote
  `git push <remote> --delete <branchname>`: To remove the old branch name in the remote
  All three commands need to be executed to rename a branch.

## Remote Branches
Remote-tracking branches are references to the state of a remote branch. They reside in the working tree and are read-only pointers which can only be updated using `git fetch`. 
![[Pasted image 20230120221420.png|480x270]]

- `git push <remote> <branchname>:<remote_branchname>` or `git push <branchname>`: Push local branch to the remote branch.
- `git checkout -b <branch> <remote/branch>` or `git checkout --track <remote/branch>` or `git checkout <remote_branchname>`: Setup tracking branch to track remote branch.
- `git fetch` + `git merge` or `git pull`: Fetch data from remote branch and merge.
- `git push <remote> --delete <remote_branchname>`: Delete remote branch 

## Rebasing
With rebasing, changes committed to one branch can be taken and replayed on top of another branch. This is an alternative way of integrating changes from one branch into another. The other option being `merge`.

- `git checkout <branchname>`+`git rebase <onto_branchname>`: Replay change-patches from `branchname` onto `onto_branchname`. Creates a new commit object equivalent to the commits from `branchname` in the `onto_branchname`. To complete the merge, `git checkout <onto_branchname>`+`git merge <branchname>`
![[Pasted image 20230121110421.png]]

Rebasing also enables selective integration of commits from any nested branches onto the main (or other branch). For example, in the following diagram the `client` branch, with `C8` and `C9` commits, is a nested branch that diverges from the `server` branch which itself is a branch from `master`. By rebasing just the `client` branch onto the `master` branch, `C8'` and `C9'` have been added as change-patches of the corresponding commits in the `client` branch.
![[Pasted image 20230121111434.png]]
### Perils of Rebasing
**Avoid rebasing commits that exist outside your repository and that others may have based their work on.**
- Only rebase commits that are local and yet to be pushed
- In case commits have been pushed, they can be rebased if no else has fetched or pulled them

### Rebase vs. Merge
Using only `merge` maintains a faithful record of what actually happened in the development process. Whereas `rebase` allows clean-up of the history to present a cleaner narrative.