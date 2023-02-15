## Revision Selection
#git 

Often, it is necessary to inspect commits in closer detail. Commits can be inspected by using the `git show <reference>` command. Here `reference` be 
- SHA-1 of the commit object: Which can be obtained by:
	- `git log`
	- `git rev-parse <branch_name>`
- the name of the branch: In which case git shows the commit information of the last commit on the branch. 
- a Reflog, which is a log of where the HEAD and branch references have been for a period of time in your local repository. This period of time is typically a couple of months. Eg.: `git show master@{yesterday}` or `git show HEAD{2.months.ago}`
- Ancestry reference: 
	- The caret symbol can be used to refer to the parent commit. Eg.: `git show <SHA-1>^`
	- The tilde symbol can be used to identify which parent. Eg.: `git show HEAD~3` will show the first parent of the first parent of the first parent
- Commit ranges:
	- Double dot: `..` is used to resolve a range of commits that can be reached by one branch but not from the other. Eg.: `git log main..experiment` shows commits that are reachable by `experiment` but not by `main`
	- Triple dot: Resolves the range of commits that are reachable by either branch but not by both of them
	- Multiple points: The `..` syntax is a short hand that is limited to two references. However, sometimes it is necessary to query multiple references. In such situations the `--not` switch  or the `^` can be used. Ex.: `git log refA refB --not refC` or `git log refA refB ^refC` both of which are equivalent and represent the range of commits that are accessible by branches refA and refB, but not by refC