##Creating a Feature branch

Most changes to source code begin with the creation of a short-lived 'feature' branch off one of the long-lived branches that exist in the repository.

The following 'long-lived' branches exist in all repositories and reflect the current state of each deployment environment:

* **master** . Source code deployed to the **production environment**.
* **uat** . Source code currently deployed to the **user acceptance testing (UAT) environment**.
* **develop**. Source code currently deployed to the **development environment**

For example:

![Bitbucket Branches](repo-branches.png)

Feature branches would normally be created off **master** as we would want to take the current state of the development and make our changes on top of that. Feature branches are short-lived and are deleted once code changes have been completed, deployed to production and merged into master. 

Feature branches should be named after the  Service Now incident, problem or enhancement record that has been created to track progress towards completion of the work. Where this isn't possible, feature branches should be given a meaningful name. 

![Bitbucket Branches](feature-branch.png)

Once created, a new feature branch can be checked out on your local machine and code changes made/committed. 