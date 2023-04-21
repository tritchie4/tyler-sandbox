## Branching/Promotion Strategy

Code branching and promotion follows the [gitflow workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) procedure.  Below are the standards for branches, their naming, code promotion, and tagging.

### Branching Standards

**Dedicated Branches**
- `main` - The branch all Production environment deployments source from.  No features should be merged to this branch, only release candidate branches or hotfix branches.
- `develop` - The branch all lower environment (Dev, Test, UAT) deployments source from.  Features should be merged to this branch, and in the event of a release the release candidate branch is cut off of this one at a certain point.

**Feature Branches**
- Any time you start a new effort for a feature or low-to-medium priority bugfix in the repository, you would create a new feature branch
  - The format for this branch name is `feature/<your-branch-name-here>` (i.e. `feature/thing-to-fix)`)
- When creating a PR for this branch, the branch you are merging into would be `develop`

**Release Candidate Branch**
- Any time you wish to initiate the promotion of features from the lower environments to Production, you would create a new branch to review those exact features and gain an idea of exactly what will be deployed after promotion.  This branch is called a release candidate branch - it is the candidate for release.
  - The format for this branch name is `release/rc-#.#.#` (`#.#.#` being the new tag number, i.e. `1.0.3`)
- When creating a PR for this branch, the branch you are merging into would be `main`

**Hotfix Branch**
- If there is an urgent issue in Production that needs fixing and must be applied quickly, you would create a hotfix branch
  - The format for this branch name is `hotfix/<your-hotfix-branch-name-here>` (i.e. `hotfix/this-urgent-bug`)
- When creating a PR for this branch, the branch you are merging into would be `main`
  
## Procedures

### New Feature to Dev/Test/UAT

<img src="https://user-images.githubusercontent.com/29279545/231491536-166f9933-43df-4355-a71f-12a76d244cd1.png" width="900" />

Often times, developers will create a new feature or fix branch intended for the Dev, Test, or UAT environment.  We typically perform testing in the ___ environment, followed by promotion to the Production environment (detailed in the next section).
1. Switch to `develop` and pull the latest changes from that branch
2. Checkout a new branch for your feature, remember to adhere to the naming standards described above (`feature/<your-branch-name-here>`)
3. Make your changes, test locally, and finally push your commits.  Deployment to Test may also occur now.
4. Open a PR, with the source being your branch and the target being `develop` (make sure it is **not** `main`)
5. Once there is at least one approval, the PR may be merged to `develop`.
6. Deployment to Test from `develop` may occur now.

### Promoting Feature(s) to Production

<img src="https://user-images.githubusercontent.com/29279545/231491634-4ee3b510-ed02-4a2e-a962-9cb3bf18e550.png" width="500" />

Ultimately once features or fixes have been tested in the lower environments, the goal is to promote and deploy the changes to Production.  In a nutshell, this involves cutting a "release candidate" branch (see description above), testing that branch's changes in a lower environment, noting the PR in the change request, merging in a PR for it, deploying to Prod, and tagging.  Here's the detailed procedure for accomplishing it:

1. Switch to `develop` and pull the latest changes from that branch
2. You will be creating a release candidate branch
  1. This will be named rc-#.#.# where the #.#.# is the new version number.  To obtain what that number should be, [check the release versions here](#needs-link) and choose the next number up.  If it's a major change, consider updating the first digit (i.e. 1.0.0 to 2.0.0), or for a medium or minor change, increment the middle or last digit (i.e. 1.0.0 to 1.1.0 or 1.0.0 to 1.0.1)
  2. Checkout a new branch for the release candidate branch, named `release/rc-#.#.#` (#.#.# being your new version number)
3. Create a PR with the source branch being your release candidate branch and the target branch being `main`. (make sure it is **not** `develop`)  Name the PR something like "rc-#.#.# to main" (where the #.#.# is your version number)
4. Test the code in the release candidate branch in a lower environment to ensure the code/changes going to production are what is intended.
5. Create a Change Request in [this form](#needs-link) and post a link to this PR in the description
6. Request reviews and once this PR has enough approvals, select "Create a Merge Commit" on the merge button (**don't** choose "Squash and Merge"), and press it to merge the branch to `main` 
7. [Create a release/tag for the promotion here](#needs-link).  This will effectively bookmark the place of this release incase we need to roll back to it for the future.  Be sure to use your #.#.# version number from above as your tag number. Include release notes in the description - [see bottom reference on how to create them](#writing-release-notes).
8. Finally (almost done), we need to catch up `develop` to `main`.  This is an important step, because `main` now has a commit that `develop` does not - the merge of the release candidate PR. (if you navigate to the [develop branch page](#needs-link), you'll see something like "This branch is # commits behind main") To remedy this:
  1. [Create a new PR here](#needs-link), the target branch being `develop` and the `source` branch being main.  Call it something like "Updating develop to latest from main"
  2. Have the PR approved and merged (using the "Create a merge commit" option) by another developer, and `develop` is now caught up!
9. Deployment to Production from `main` may occur now.

### Promoting Hotfix to Production

<img src="https://user-images.githubusercontent.com/29279545/231491583-fee9932a-1c59-4ff0-9fd6-4481c808ebaf.png" width="900" />

If there is an urgent issue in Production that needs fixing and must be applied quickly, you would create a hotfix branch.  

1. Switch to `main` and pull the latest changes from that branch
2. Checkout a new branch for your hotfix, remember to adhere to the naming standards described above (`hotfix/<your-hotfix-branch-name-here>`)
3. Make your changes, test locally if possible, and finally push your commits.
4. Create a PR with the source branch being your hotfix branch and the target branch being `main`. (make sure it is **not** `develop`)  Name the PR something like "hotfix to main"
5. Create a Change Request in [this form](#needs-link) and post a link to this PR in the description
6. Request reviews and once this PR has enough approvals, select "Create a Merge Commit" on the merge button (**don't** choose "Squash and Merge"), and press it to merge the branch to `main` 
7. [Create a release/tag for the promotion here](#needs-link).  This will effectively bookmark the place of this release incase we need to roll back to it for the future.  Be sure to use your #.#.# version number from above as your tag number.  Include release notes in the description - [see bottom reference on how to create them](#writing-release-notes).
8. Finally (almost done), we need to catch up `develop` to `main`.  This is an important step, because `main` now has a commit that `develop` does not - the merge of the release candidate PR. (if you navigate to the [develop branch page](#needs-link), you'll see something like "This branch is # commits behind main") To remedy this:
  1. [Create a new PR here](#needs-link), the target branch being `develop` and the `source` branch being main.  Call it something like "Updating develop to latest from main"
  2. Have the PR approved and merged (using the "Create a merge commit" option) by another developer, and `develop` is now caught up!
9. Deployment to Production from `main` may occur now.


# Appendix

## Writing Release Notes
It is helpful to include release notes on a given [release/tag](#needs-link) to indicate to other developers which PR's and changes were included.  This guide assumes you have already merged the release candidate branch into `main` and are now looking to create the release/tag.  Here is an effective way to generate release notes:
1. Navigate to the [release candidate PR](#needs-link) that was approved/merged for the release.
2. In the commit history, you should see squashed commits for each PR that was included in the release (in other words, you should see one or more lines like this example: `Grand merge of DD Formatting Changes (#628)`)
3. Copy/paste that list of lines into the Description of your new release/tag and format it to your liking.  These will serve as your release notes and help inform other developers of what went into the release.
