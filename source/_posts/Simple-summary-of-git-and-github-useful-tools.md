---
title: Simple summary of git and github useful tools
date: 2020-10-06 09:02:47
tags:
---

### Git Tools - Submodules

It often happens that while working on one project, you need to use another project from within it. It's like having multiple microservice projects and all rely on the same data model, this makes it very easy to manage the data model.

#### How to use it

`.gitmodules` is a configuration file that stores the mapping between the project's URL and the local subdirectory you arre pulled it into:

```
[submodule "models"]
	path = src/models
	url = https://github.com/xxxx/project_models.git
```

You can load the submodule with :

```
git submodule update --init
```

You can add commit and push the change in the submodule directory. When you want to update submudle  

can run:

```
git submodule update
```

### Github - Creating a repository from a template

Template repository enable developers to mark a repository as a template, which can use later for creating new repositores containing all of the template repository's file and folders. This feature is very useful and convenient when developing projects of the same type.

You can create a new template repository or mark an existing one as a template with admin permissions. Just navigate to the Settings page and then click on the **Template repository** checkbox. Once the template repository is created anyone who has access to it will be able to generate a new repository with same directory structure and files via‘**Use this template** button.