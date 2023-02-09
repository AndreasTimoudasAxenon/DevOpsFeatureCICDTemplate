# Axenon DevOps Template for new orgs


## Setup

To setup a repository for a new org with the workflows, one can create a new repo from tempalate and use this one.
After that is done, go to `Settings` and add the Salesforce AuthUrl as `secrets` under the names `SFDX_DEV_URL`, `SFDX_QA_URL` and `SFDX_PROD_URL` with the respective enviroment AuthUrl under each secret. Once that is done the workflows will be able to auth against each environment. To get the secrets:

`sfdx force:org:display -u <ALIAS> --verbose` will generate the Sfdx Auth Url
The URL has format `"force://<clientId>:<clientSecret>:<refreshToken>@<instanceUrl>"`.

If QA is not needed than the QA related jobs can be disabled. Either way the jobs will not be triggered since the
qa branch will not exist.
# Expected WorkFlow

## Source of truth

The `development`  branch is expected to be the source of truth. Each new feature should branch out from the `development` branch.

## New Feature

In the starting of a new feature a branch should be created from the `development` branch.
The development of the feature is then done in a local enviorment, where unittests are also performed. 

Once the feature is developed and ready for for User Acceptance Tests (UAT), a pull request is first opened against the `developement` branch. This performs regression testing and if the tests pass it merges the feature into the `development` branch and opens a pull request against the UAT branch where the user can test before deployment to production.

If the user is satisfied with the testing the pull request can be closed and merged into the UAT
branch. This triggers a downward merge of the UAT branch to the `development` branch. Once this downward merge is performed each developer working on a feature is responsible to rebase their feature branch from the development branch.

# Github Actions

1. Validate PR on dev
 + On: `Pull Request`
 + Types: `opened`, `synchronize`, `edited`
 + branch: `dev`
 + checked source: `force-app/**`

2. Deploy dev branch to dev org, open UAT PR
 + On: `push`
 + branch: `dev`
 + checked paths: `force-app/**`
 + method: `sfdx-git-delta`

3. 