## Reset Demystified
#git 

An easy way to understand the many operations of `reset` is by thinking of Git as a system that manipulates three different trees. A tree can be thought of as a "collection of files".

|Tree | Role |
|:-----|:----|
| HEAD | Last commit snapshot; next parent |
| Index | Proposed next commit snapshot |
| Working Directory | Sandbox |

Based on the switch provided to the command, the reset command stops the reset at particular stages. The image below represents this.
1. `git reset --soft HEAD~`: Only move HEAD to point to parent commit.
2. `git reset --mixed HEAD~`: Move HEAD and reset file in index (staging area) to file version in parent commit.
3. `git reset --hard HEAD~`: Move HEAD, reset file in index, and change file in working directory to file version in parent commit. File in working directory gets overridden and becomes unrecoverable.

![[Pasted image 20230205161735.png]]

### Reset with a Path
Reset can also be used on file names or paths to reset the file to a previous version.
- `git reset --<switch> <commit> <file>`: Resets `file` in a tree identified by `switch` to the version in `commit`.

### Comparison with Checkout
Git Reset has many features similar to Checkout. However, the underlying mechanisms have some key differences. The differences are summarized in the following table.

|                       | HEAD | Index | Working Directory (WD) | WD Safe? |
| -------------------------- | ---- | ----- | ---------------------- | -------- |
| **Commit Level**           |      |       |                        |          |
| `--soft <commit>`          | REF  | NO    | NO                     | YES      |
| `--mixed <commit>`        | REF  | YES   | NO                     | YES      |
| `--hard <commit>`          | REF  | YES   | YES                    | **NO**   |
| `checkout <commit>`        | HEAD | YES   | YES                    | YES      |
| **File Level**             |      |       |                        |          |
| `reset <commit> <file>`    | NO   | YES   | NO                     | YES      |
| `checkout <commit> <file>` | NO   | YES   | YES                    | **NO**         |

Here REF implies the reference for the HEAD pointer itself moves.