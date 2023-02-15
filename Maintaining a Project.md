# Maintaining a Project
#git 

- The maintainer of a project needs to namespace branches in the project. For example, `git branch sc/ruby_client`, where `sc` is short for the person who contributed to the work. 
- `git diff main...<topic_branch>`: While integrating the work done on a topic branch, it is useful to understand what is introduced by the changes. This can be done by checking he `diff` between the last commit on the topic branch and the common ancestor with the `main` or a related branch from which the topic branch diverged. Git provides a convenience command for doing this.
- `git archive main --prefix='project/' | gzip > `git describe master`.tar.gz`: Archive a project for release as a compressed zipped folder.
- `git shortlog --no-merges main --not <prev_version>`: Get a clean summary of all the commits since `prev_version` grouped by author. This can be used to email subscribers about the changes in the latest release.

When dealing with larger or stable projects it is good to be cautious about the changes being introduced into the `main` branch. In these settings, the use of a `main` branch for maintaining releases and a `develop` branch for development is recommended. All changes from topic branches are merged into the `develop` branch first, where they are verified and merged into the `main` branch. In addition, the releases can be tagged or prepared as a compressed folder for developers who do not use Git.