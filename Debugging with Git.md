## Debugging with Git
#git 

### File Annotation
File annotation allows tracking down of bugs by showing what commits modified each line of any file in the repository.

-`git blame`: determine which commit introduced a line

Further, Git can track code movement of code snippets between files. This can help figure out where the original code came from.

### Binary Search
When the commit where the bug was introduced is unknown and there have been many subsequent commits, `git bisect` can help narrow down the faulty commit. It uses a binary search to find the bug quickly.
- `git bisect start`: Start binary search.
- `git bisect bad`: Set the current commit as the broken commit.
- `git bisect good <commit>`: Set last known good commit. The command also does a checkout of the middle commit in between the `bad` and the `good` commits.
- `git bisect good`: Indicate that the middle commit which has been checked out is good. Checkout the middle commit of the second half of the entire set of commits.
- `git bisect bad`: Indicate that the middle commit which has been checked out is bad. Checkout the middle commit of the first half of the entire set of commits.
- `git bisect reset`: Reset HEAD to position before start of bisect.