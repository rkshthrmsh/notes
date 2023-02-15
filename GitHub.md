# [[Git]]Hub
#git 

GitHub is the largest collection of git repositories and serves as a platform for collaboration for millions of developers and projects.

## Contributing to a Project
The most popular flow for contributing to a GitHub project involves the following steps:
1. Fork the project
2. Create a topic branch on the `main` branch
3. Make necessary commits
4. Push branch to GitHub remote
5. Open a Pull Request
6. Discuss and iterate, if necessary
7. Project owner or maintainer merges the project and closes the Pull Request
8. Sync the updated `main` back to the fork

### Pull Requests
In comparison to email based patching systems, which tend to think of patches as a perfect series of contributions, pull requests are meant to be an iterative conversation around a proposed change. They enable early conversations and reviews with maintainers so that the change is community driven. Moreover, the iterative conversation is captured as a series of commits in the pull request thereby maintaining a history and context which can be revisited if necessary.

Pull Requests that are created by contributors on a project appear as pseudo-branches on the project. For example, in the following image, `refs/pull/1/head` refers to a pull request created by a contributor. The `git ls-remote` command can be used to view all the commits in a repository. Normally, git only fetches the branches under `refs/heads` when the repository is cloned. By doing `git fetch origin refs/pull/<#number>/head`, one can fetch the pull request related commits directly from remote.

![[Pasted image 20230203140410.png]]

Another way to fetch all pull requests is by adding a Refspec to the `.git/config` as shown in the image. This automatically fetches all the pull requests to the local repository.

![[Pasted image 20230203141250.png]]