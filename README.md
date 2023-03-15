# Combine PRs
This GitHub Workflow allows you to combine a number of PRs into a single one, by merging the branches for each PR into a new branch and opening a PR for it. Specifically, it was created to group Dependabot PRs together, but it is generic enough that it can be used with other services that create PRs with branch names that have a specific prefix (such as `dependabot/{branchname}` in the case of dependabot).

![Combined PR](/images/combined-pr.png?raw=true "Combined PR")

# Get started
To use this, copy [combine-prs.yml](combine-prs.yml) into your repository to the folder `.github/workflows/` and commit and push it to GitHub.
Then on GitHub, click the 'Actions' tab at the top of your repository, then under 'All workflows' on the left click 'Combine PRs', and finally on the righthand side, click 'Run workflow'.

![Run workflow](/images/run.png?raw=true "Run workflow")

⚠️ If you don't run checks on your repo, set mustBeGreen to false (consider setting it to default to false in the yml file, so you don't have to remember to change it to false on every run)

# Customization
The workflow uses only the [actions/github-script](https://github.com/actions/github-script/) action published by GitHub.
As you can see from the image you can easily set another branch prefix for matching, and choose whether to only include branches that are green (have the 'success' status on GitHub, e.g. from successful CI) - this is the default. If you don't run checks, then set this setting to false, since a PR with no checks will have a status of 'pending' rather than 'success'.

Finally, you can set a label on PRs that you want to exclude when combining PRs - by default this label is 'nocombine'.

You are also welcome to modify the code to your needs if you need more customization than that (for example it currently doesn't work with forks, only branches within the same repo).

# Limitations
This workflow merges the branches of the PRs together into a single branch using git's simple merge and automatic merge strategies. Unfortunately that means it has the same limitations as these merge strategies, so it will only work on PRs with branches that can be auto-merged without running into a merge conflict. In case any merge conflicts happen, the created combined PR will just include as many branches as could be merged without conflict, and it will list which PRs were left out due to merge conflicts.
The way merge conflicts will typically happen when updating dependencies is that different dependency updates end up modifying the same line in the `.lock` file (the file that ensures stability in exactly which versions of depencies are currently being used, when dependencies are defined in a broad enough way that multiple different versions could satisfy the constraints).
This typically happens if they share some common third dependency - for example if some modular framework has components A and B, that both depend on C, then updating A and B independently might lead to also indirectly updating C to two different versions in the two branches, and that will prevent them from being mergable.

The "correct" solution here is to add both dependencies together, then let the package manager resolve the dependencies to hopefully find a version of C to put in the `.lock` file that will satisfy both A and B.
If you find yourself in need of this, then this workflow won't help you, and you should consider switching to a dependency update service that will update dependencies together like that (at the moment *Dependabot* does not, but *Depfu* and *Renovate* do).
