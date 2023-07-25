## Submodules
#git 

Using submodules a Git repository can be kept as a subdirectory within another Git repository. The submodule repository can be cloned into the repository while still maintaining commits from the user repository separately.

- `git submodule add <URL>`: Add a submodule to an existing Git project. This creates a new `.gitmodules` file that maintains a list of all submodules in the project. Git does not track the content of the submodule directory unless local edits are made within it.
- `git clone <URL>` + `git submodule init` + `git submodule update`: All three commands (clone main repository, initialize configuration file, and update submodules) need to be run to clone a project with submodules.
  `git clone <URL>` + `git submodule update --init --recursive`: Another way of cloning, initializing and updating submodules.
  `git clone --recurse-submodules`: Automatically clone, initialize, and update submodules.
- `git diff --submodule`: Diff submodules. Switch can be run every time by updating `.git/config` variable `diff.submodule` to `log`.
- `git fetch` + `git merge`: To update submodule fetch and merge submodule from the subdirectory.
  `git submodule update --remote <submodule>`: Git automatically goes into `submodule` subdirectory and fetches and merges the latest changes. Skip `<submodule>` to update all submodules within a Git repository. Without modification, Git tracks the default branch of the remote submodule. However, if another branch needs to be tracked, `.gitmodules` can be updated to reflect the same.
- `git status`: The status command can be used to show the status of the submodule changes by setting the `status.submodulesummary` in `.git/config`.
- `git pull` + `git submodule update --init --recursive`: Pull upstream changes from remote and update all submodules.
  `git pull --recurse-submodules`: Single command to pull and run `git submodule update` right after the pull.
  - `git submodule sync --recursive`: Required to sync submodule references in situations where the repository URL is updated.
  - `git submodule foreach '<command>'`: Run `command` on all submodules within a git repo.

By default, submodule states are not carried over when switching branches. Using `--recurse-submodule` ensures that submodules are placed in the right state when switching into branches.