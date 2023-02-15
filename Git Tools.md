# [[Git]] Tools
#git 

Git provides a set of powerful tools which can be used for advanced manipulation of repositories.

## [[Revision Selection]]

## Interactive Staging
Git has provision for interactive staging which can be used for crafting commits. Sometimes, when a large amount of work has been done without committing any of it, interactive staging can help break it up into smaller focused commits that can be reviewed easily and segregated into logical changesets. The command for this is `git add -i` or `--interactive`.

## [[Stashing and Cleaning]]

## Searching
### Git Grep
- `git grep`: Is a powerful, fast search command that enables not only searching through the working directory but also any tree in Git. It offers a plethora of options to conduct complicated searches.
### Git Log
- `git log`: Enables searching for *when* a pattern was introduced or existed as opposed to *where* it exists. Git log can also be used to search through the history of a file.

## [[Rewriting History]]

## [[Reset Demystified]]

## [[Rerere]]

## [[Debugging with Git]]

## [[Submodules]]

## Replace
The objects in Git are unchangeable, but Git provides a technique to pretend that an object can be replaced. By using the `replace` command, every reference to an object can be directed to another object.

- `git replace <commitA> <commitB>`: Replace `commitA` with `commitB`.