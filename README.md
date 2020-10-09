# Combine PRs
This GitHub Workflow allows you to combine a number of PRs into a single one, by merging the branches for each PR into a new branch and opening a PR for it. Specifally, it was created to group Dependabot PRs together, but it is generic enough that it can be used with other services that create PRs with branch names that  have a specific prefix (such as dependabot/{branchname} in the case of dependabot).

# Get started
To use this, copy [combine-prs.yml](combine-prs.yml) into your repository in the folder .github/workflows/ and commit and push it to GitHub. Then on GitHub, click the 'Actions' tab at the top of your repository, then under 'All workflows' on the left click 'Combine PRs', and finally on the righthand side, click 'Run workflow'.

# Customization
The workflow uses only the [actions/github-script](https://github.com/actions/github-script/) and [actions/checkout](https://github.com/actions/checkout/) actions published by GitHub. As you can see from the image you can easily set another branch prefix for matching, and choose whether to only include branches that are green (have the 'success' status on GitHub, e.g. from successful CI) - this is the default (if you don't run checks, then set this to false, since no checks will have a status of 'pending' rather than 'success'). You are also welcome to modify the code to your needs if you need more customization than that (for example it currently doesn't work with forks, only branches within the same repo).
