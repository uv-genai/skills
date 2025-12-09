---
name: uv-gen
description: Use this skill to generate a python uv project, specifying name, description and python version.
---

Prompt for information asking ONE BY ONE the following questions.

# 1. Prompt for project name

- Prompt for project name
- wait for user input and store it in variable 'project_name'.

# 2. Prompt for project description

- Prompt for project description
- wait for user input and store it in variable 'project_description'.

# 3. Prompt for python version

- Prompt for python version
- wait for user imput and store it in variable 'python_version', use current python version if not provided.

# 4. Set author

- Set author to current user.

# Generate project

Generate uv project in the current directory using the project name, project description and python version provided by the user:

```bash
uv init --name project_name --python python_version --description project_description --bare
```


