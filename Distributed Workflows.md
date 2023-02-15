# Distributed Workflows
#git 

Owing to the flexibility offered by Git's distributed version control system and the ease of branching, many different workflows are possible. The most common ones include:

## Centralized Workflow
In this workflow, one centralized repository accepts code and all developers synchronize their development with this repository. When any developer pushes a change to the central repository, other developers would need to fetch and merge before being able to push their own changes.
![[Pasted image 20230125165035.png]]

## Integration-Manager Workflow
In this workflow, Git's ability to have multiple remote repositories is utilized to create a public repository for each developer on the project. Developers have write access to their own public repositories and read access to other developers' repositories. In addition, there exists a canonical repository that represents the "main" project. Only a maintainer has write access to the repository. Developers fork or clone the canonical repository, make changes, and push them to their own public repositories. The maintainer adds the developer's repository as a remote, tests the changes locally, merges it into their branch, and pushes to the canonical repository. 

The key advantage here is that the developer can continue to work on their repository without worrying about fetching and merging changes by other developers. Likewise, the maintainer can pull in changes independent of the developers' work. 

![[Pasted image 20230125165120.png]]

## Dictator and Lieutenant Workflow
Huge projects with multiple contributors and hierarchy use this variation of the multi-repository workflow. Developers have their own public repositories that are rebased from a central canonical "main" project. All changes are made on developers' topic branches which are merged into lieutenants' main branches after local testing by the lieutenants. A dictator merges lieutenants' main branch with their own and eventually pushes to the canonical repository. All the developers then rebase their work on the latest updates of the canonical repository. 

The workflow is useful in projects with a large number of contributors and where hierarchy is preferred. Delegating work becomes easier because of the dictator-->lieutenant-->developer hierarchy.



Keeping `main` healthy is important because it makes it easier for new employees to start developing code.