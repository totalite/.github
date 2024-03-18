# TotaLite 
This is the github organisation of the TotaLite team. All of the code that goes in to making the TotaLite solution work, from sensor firmware to client dashboards, is hosted in a repository here.
The rest of this readme will contain our way of working with git(hub). If you are not a contributor this will probably not be of interest to you.

## Repositories
All repositories should be named in this format: `totalite.max_three_words`. For example the repository that contains the code that can talk to our sensor hardware is named `totalite.sensor_interface_library`.
All repositories must follow these rules:
+ All repositories must be private
+ All repositories must include a `.gitignore` file
+ All repositories must include a `.gitattributes` file that specifies the line ending conventions for that repo (force LF if appropriate for the language)
+ The `main` branch must be used as the default branch and must be protected
  + it must require an approved pr before merging

## CI/CD
We use github actions as our CI/CD system. Every repository that needs to run actions on build or in reponse to an action in git must use github actions to do so.
We expect repositories to have some of these actions:
+ `build_test.yml` an action that runs a build (if applicable), linting, unit tests, and integration tests
  + this should run on push
+ `deploy.yml` an action that deploys the architecture required to run the project. For packages this can be omitted
  + this action should run on manual dispatch
  + the deploy should have a unique identifier passed as an argument to the build so it can be referenced again in the next action
+ `destroy.yml` an action that tears down the deployed architecture
  + this action should run on manual dispatch
  + it should only destroy archtecture for a specific id
  + this action should not be allowed to run in production environments

 ## Git workflow
 ### development
 The `main` branch of each repository is the default branch where all new branches should start and eventually be merged back in. Merging a feature back in to main should only happen after a code review. You are free to decide if you want to squash-rebase or create a merge commit. We do not allow rebase-merge since that will produce an unclear history in the `main` branch.
 The full git workflow of a feature should follow these steps:
 + create a ticket in jira (TO DO: rewrite when we switch to linear). This ticket will get a number like TOTIOT-9999
 + create a branch from main:
```
> git checkout main
> git pull
> git checkout -b TOTIOT-9999-short-description
```
+ create your commits. If you are not going to squash do take care to format your commit messages like `TOTIOT-9999: message`. This way it is not necessary to trace a commit all the way to its merge into main to figure out for which ticket it was done
```
> git add --all
> git commit -m "TOTIOT-9999: message"
> git push
```
+ create a PR from the github web page
+ apply any review comments
+ when the PR is approved choose either `Create a merge commit` or `Squash and merge` from the merge button on the PR page
### Acceptance and release
We identify acceptance and release versions by tags on the main branch. Commits tagged like `X.Y.Z-rc1` are release candidates (candidate 1 for version X.Y.Z in this case). Commits tagged with just a version number like `X.Y.Z` are releases.

Commits are tagged as a release candidate once all features for a release have been merged into `main` and the acceptance test passed. If acceptance fails some fixes are made and then the release is tagged with a higher `rc` number. When acceptance passes the commit is tagged with the version number.
