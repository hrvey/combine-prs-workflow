# Combine PRs
This GitHub Workflow allows you to combine a number of PRs into a single one, by merging the branches for each PR into a new branch and opening a PR for it. Specifically, it was created to group Dependabot PRs together, but it is generic enough that it can be used with other services that create PRs with branch names that have a specific prefix (such as `dependabot/{branchname}` in the case of dependabot).

![Combined PR](/images/combined-pr.png?raw=true "Combined PR")

# Get started
To use this, copy [combine-prs.yml](combine-prs.yml) into your repository to the folder `.github/workflows/` and commit and push it to GitHub.
Then on GitHub, click the 'Actions' tab at the top of your repository, then under 'All workflows' on the left click 'Combine PRs', and finally on the righthand side, click 'Run workflow'.

![Run workflow](/images/run.png?raw=true "Run workflow")

# Customization
The workflow uses only the [actions/github-script](https://github.com/actions/github-script/) and [actions/checkout](https://github.com/actions/checkout/) actions published by GitHub.
As you can see from the image you can easily set another branch prefix for matching, and choose whether to only include branches that are green (have the 'success' status on GitHub, e.g. from successful CI) - this is the default. If you don't run checks, then set this setting to false, since a PR with no checks will have a status of 'pending' rather than 'success'.

Finally, you can set a label on PRs that you want to exclude when combining PRs - by default this label is 'nocombine'. For example this can be handy if you have a single PR causing merge conflicts (see Limitations below) - in that case you can exclude that PR by adding the label, and still combine the rest of the PRs.

You are also welcome to modify the code to your needs if you need more customization than that (for example it currently doesn't work with forks, only branches within the same repo).

# Limitations
This workflow merges the branches of the PRs together into a single branch using git's simple merge and automatic merge strategies. Unfortunately that means it has the same limitations as these merge strategies, so it will fail if two or more PRs have branches that cannot be auto-merged due to a merge conflict - it will say something along the lines of "Auto-merge failed" or "Should not be doing an octopus" (see an example [here](https://github.com/hrvey/combine-prs-workflow/issues/2)).
The way this will typically happen when updating dependencies is that different dependency updates end up modifying the same line in the `.lock` file (the file that ensures stability in exactly which versions of depencies are currently being used, when dependencies are defined in a broad enough way that multiple different versions could satisfy the constraints).
This typically happens if they share some common third dependency - for example if some modular framework has components A and B, that both depend on C, then updating A and B independently might lead to also indirectly updating C to two different versions in the two branches, and that will prevent them from being mergable.

The "correct" solution here is to add both dependencies together, then let the package manager resolve the dependencies to hopefully find a version of C to put in the `.lock` file that will satisfy both A and B.
If you find yourself in need of this, then this workflow won't help you, and you should consider switching to a dependency update service that will update dependencies together like that (at the moment *Dependabot* does not, but *Depfu* and *Renovate* do).

If it's only a single PR out of several that is causing merge issues, you can use the label 'nocombine' (see above) to merge the others together, then merge the last one alone.
