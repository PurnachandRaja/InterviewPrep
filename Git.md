### 1.git cherry pick 
Let us say we have lot of commits in your feature branch but I only wanted to merge specific commits to the main then I can use git cherry pick to merge specific commits
Move to main bracnch and run below command

```
git cherry-pick commitSha
```

### 2.git rebase 
Let us take we have 1 commit in main branch. I created a new branch featureX out of it and made two commits. But by the time i merge this changes to main there is someone else in team made a new commit to main branch. Now if i push my changes and raise PR it will give me merge conflicts so to overcome that we can rebase our featureX branch with main which will rebase to commit 2 instead of commit 1 which we had when we initially created branch featureX. Now we will obviously get conflicts so what we can do is open the code in vscode compare and accept both changes and then add and do a git rebase --continue

```
git rebase main
code jl.csv
git rebase --continue
```

### 3.What is your branching strategy?
We use GitFlow strategy. Basically we cut develop from main and feature branch from develop. Once the changes are done a PR will be raised to develop which will run Build Pipeline as we configured Hooks. Once the build pipeline and all the other required checks are passed the reviewers can verify and merge the code. 
Now there is Release pipeline which is responsible for merging code to main so no one touches main. The main is the source of truth for production. 
For Bug fixes/ Emergency changes we cut branch directly from main and once it is ready we will run Release Pipeline which will merge code to main and once it is done we also rebase this branch with develop and resolve the merge conflicts before same changes are pushed to develop.

### 4.One of your colleagues accidentally pushed a commit with sensitive information to a public repository. What steps would you take to remove the commit from the repository and prevent similar incidents from happening in the future?
To remove a commit with sensitive information from a public repository, I would use Git's `git revert` command to undo the commit, and then git push the changes to the repository. To prevent similar incidents from happening in the future, I would implement a code review process, use Git's .gitignore file to exclude sensitive information, and provide training and guidelines to team members on proper Git usage and security measures.

### 5.Git rebase vs merge
Git merge combines the changes from different branches into a new commit, while keeping the original branch history. Git rebase integrates the changes from one branch onto another by reapplying each commit on top of the destination branch, resulting in a linear history.

In summary, merge preserves branch history and is useful for integrating independent branches, while rebase creates a linear history and is useful for keeping feature branches up to date with the main branch.



