# Git
#git

Git is a Distributed Version Control System (DVCS) [1]. In a DVCS, clients fully mirror the repository, including its full history. This provides additional protection. In situations where a server dies, any of the local client repositories can be copied back to the server to restore it. Development of Git began as a version control tool for the Linux kernel project, and its original goals were: 
- Speed
- Simple design
- Strong support for non-linear development (parallel branches)
- Fully distributed system
- Ability to handle large projects.

While other VCS tools store information as a list of file-based changes (delta-based version control), Git thinks of data as a series of snapshots. With each commit, Git takes a "picture" of the files at the moment and stores a reference to that snapshot. If files have not changed, they are not stored again, but are instead linked to the previous identical file. This is the biggest difference between Git and other VCS.

## Key Features
1. Nearly Every Operation is Local
	1. Most operations seem instantaneous since the entire project history is there on the local disk.
	2. Easy to work offline
2. Git has Integrity
	1. Git uses SHA-1 hash for calculating checksums of everything, making it impossible to lose information or face file corruption without being able to detect it. The checksum is a 40-character hexadecimal string.
3. Git Generally Only Adds Data
	1. Most action are undoable and it is difficult to lose data. This make it easy to experiment without the risk of losing data.

## Three Sections of a Git Project
Each git project has three sections.
1. Working Tree: Is a checked-out version of a project. It is local to the client and a section where files are modified.
2. Staging Area: Is a file in the Git directory which contains information about what will be in the next commit. Technical name is *Index*. Only changes that will go into the database are stored here.
3. Git Directory: Is where Git stores the metadata and object database of the project and is what is copied when a repository is *cloned*. A commit takes files, as they are, from the staging area and stores the snapshot permanently to the Git directory.

## The States
All files in Git can only exist in one of two states.
1. Untracked: Files in the working tree that were not in the last snapshot nor in the staging area.
2. Tracked: Files that were in the last commit snapshot as well as any newly staged files.
	1. Modified: The file has changed but not committed to the database yet.
	2. Staged: A file that has been marked as modified and is staged to go into the next commit snapshot. Git stages a file exactly as it is at the time of staging it. Further modifications to the same file will need to be re-staged.
	3. Committed (Unmodified): The data is safely stored in a local database, and the file has not been modified since last commit.

![[Pasted image 20230118165514.png]]

Files that need not be a part of the Git Directory can be ignored by creating a `.gitignore` file.

## References
[1] S. Chacon and B. Straub, _Pro Git_. Berkeley, CA: Apress, 2014. doi: [10.1007/978-1-4842-0076-6](https://doi.org/10.1007/978-1-4842-0076-6).

