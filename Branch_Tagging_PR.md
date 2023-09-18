## How do we create a branch, tag the branch, and create a PR to merge the branch?

- We want to open a new issue that explains the feature we are trying to implement to our main branch.

![screenshot](assets/GitHub_Issue.png)
> Screenshot of a Github issue made.

- Now that the feature or issue is made we want to create a branch and switch to it, we can do so using this command.

```bash
git checkout -b <name_of_branch>
```
> The ```git checkout -b``` command creates a new branch and checks out to it in one step. This can be useful when you want to start working on a new feature or bug fix without leaving your current branch. To use the command, simply specify the name of the new branch after the ```-b``` option.

- Having to keep up with our changes we want to ```git push``` to our Github but first have to set the branch to upstream.

```bash
git push --set-upstream origin
```

![screenshot](assets/upstream_command.png)
> The ```git push --set-upstream origin``` command pushes your local branch to the remote repository named origin and sets it as the upstream branch. This means that when you run git push without any arguments, git will push your local branch to the remote upstream branch.