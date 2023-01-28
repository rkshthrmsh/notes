# Distributed [[Git]]
#git 

## Distributed Workflows
Owing to the flexibility offered by Git's distributed version control system and the ease of branching, many different workflows are possible. The most common ones include:

### Centralized Workflow
In this workflow, one centralized repository accepts code and all developers synchronize their development with this repository. When any developer pushes a change to the central repository, other developers would need to fetch and merge before being able to push their own changes.
![[Pasted image 20230125165035.png]]

### Integration-Manager Workflow
In this workflow, Git's ability to have multiple remote repositories is utilized to create a public repository for each developer on the project. Developers have write access to their own public repositories and read access to other developers' repositories. In addition, there exists a canonical repository that represents the "main" project. Only a maintainer has write access to the repository. Developers fork or clone the canonical repository, make changes, and push them to their own public repositories. The maintainer adds the developer's repository as a remote, tests the changes locally, merges it into their branch, and pushes to the canonical repository. 

The key advantage here is that the developer can continue to work on their repository without worrying about fetching and merging changes by other developers. Likewise, the maintainer can pull in changes independent of the developers' work. 

![[Pasted image 20230125165120.png]]

### Dictator and Lieutenant Workflow
Huge projects with multiple contributors and hierarchy use this variation of the multi-repository workflow. Developers have their own public repositories that are rebased from a central canonical "main" project. All changes are made on developers' topic branches which are merged into lieutenants' main branches after local testing by the lieutenants. A dictator merges lieutenants' main branch with their own and eventually pushes to the canonical repository. All the developers then rebase their work on the latest updates of the canonical repository. 

The workflow is useful in projects with a large number of contributors and where hierarchy is preferred. Delegating work becomes easier because of the dictator-->lieutenant-->developer hierarchy.



Keeping `main` healthy is important because it makes it easier for new employees to start developing code.

## Contributing to a Project
The method for contributing to a project depends on the number of active contributors, Git workflow, developers' commit access, and external contribution methods.
- The size of the active contributor team determines the merge complexity and code consistency.
- The workflow determines the process for merging the developers' code into the "main" project branch--whether they do it directly or through a lieutenant--and also the code review process.
- The commit access and frequency are also important variables. Whether developers have write access to the central repository, or accepted methodology for pushing changes determine the process of contributing to a project.

## Commit Guidelines
Each commit should be a logically separate changeset. The staging area can be used to separate large chunks of changes to digestible commits, each with its own commit message. Files can be staged partially using Interactive Staging. Rewriting History is another technique to clean up the commit history to make the end result more tractable.

Most importantly, commit messages need to follow a standard high-quality template to ensure that code revisit and collaboration become easier. As a general rule, ensure that the commit messages are composed of a summary line (or title) that captures the purpose of the changeset concisely and then a detailed explanation including the motivation for the change and a comparison with the previous implementation. Finally commit messages should be written in the imperative: "Fix bug" instead of "Fixed bug" or "Fixes bug".

```text
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary.  Wrap it to about 72
characters or so.  In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body.  The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase will confuse you if you run the
two together.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug."  This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Typically a hyphen or asterisk is used for the bullet, followed by a
  single space, with blank lines in between, but conventions vary here

- Use a hanging indent
```
## Integration Frequency
The frequency at which code is pushed in to the remote repository determines the efficiency of resolving conflicts, the complexity of each merge, and the risks associated with them. High frequency integration, in general, is advantageous. Although it increases the frequency of merging, the size of each merge is usually small which reduces complexity, risk, and helps identify conflicts earlier in the development process. Refactoring is also much simpler with a higher frequency of integration since it can be done regularly with little friction. 
![[Pasted image 20230123161557.png]]
