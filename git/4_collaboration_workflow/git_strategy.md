---
title: 1. Git Strategy
---

# Git Strategy

## Feature-branching Strategy
TBC: Talk about feature branching strategy

TBC: Talk about possible preference for **REBASE** vs **MERGE**

## Git Workflow
1. **Create an Issue**  
   Open the [GitLab Kanban board](https://gitlab.aisingapore.net/aisg/100e-projects/aiap16/l-q-oasis-aut0/motextmotion/-/boards) and create a new issue for the task at hand.

2. **Branch Naming**  
   [Create a branch](https://gitlab.aisingapore.net/aisg/100e-projects/aiap16/l-q-oasis-aut0/motextmotion/-/branches/new) using the following naming convention:  
   ```plaintext
   {issue-number}-{component}-{issue-name} 
   ```  
   Example:  ```84-pipeline-humanml3d```

3. **Fetch Remote Branch**  
   Update your local repository with the latest changes from the remote repository:  
   ```zsh
   git fetch
   # or
   git pull
   ```

4. **Create and Track Remote Branch**  
   First, check the available remote branches to ensure you've pulled the latest updates:
   ```zsh
   git branch -a
   ```
   This command will display all local and remote branches.
   Next, create a new local branch that tracks the corresponding remote branch:
   ```zsh
   git checkout -b {your-branch} origin/{branch-name}
   ```

5. **Work on Features**  
   Implement your changes and work on the features within your local branch.

6. **Stage and Commit Changes**  
   Stage your changes and commit them with a descriptive message:  
   ```zsh
   git add .
   git commit -m "Brief description of changes"
   ```
   Refer to [below](#git-commit-message) for naming conventions for commit messages.

7. **Rebase Before Merging**  
   Before merging, rebase your branch onto the main branch to ensure it is up to date:  
   ```zsh
   git checkout main
   git pull # Ensure that your local main branch has the latest updates
   git checkout {your-branch}
   git rebase main
   ```

8. **Submit a Merge Request**  
   Once your branch is ready, navigate to GitLab and [submit a merge request](https://gitlab.aisingapore.net/aisg/100e-projects/aiap16/l-q-oasis-aut0/motextmotion/-/merge_requests/new) for review.