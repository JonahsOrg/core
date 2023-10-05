# Core

The Master Repository ("Core") centralizes by referencing sub-repositories. It automates submodule creation, tracks changes, and compiles code for release. Rollback mechanisms ensure stability, while documentation provides clarity. The final code is released to production.


## Core branches

| Branch Name | Purpose | Description |
|-------------|---------|-------------|
| `main` or `production` | Production | This is the stable branch that represents what's currently in production. It should be deployable at any time. |
| `develop` or `staging` | Integration | This branch serves as an integration branch for features and fixes. It's where you'd automatically pull in changes from submodules and ensure everything works together. |
| `hotfix/*` | Quick Fixes | For urgent fixes that need to be applied directly to the production code. |
| `release/*` | Pre-release | When preparing a new production release. This branch allows for final testing and any meta-level changes needed for a release (like updating documentation, version numbers, etc.). |


## Submodule branches

| Branch Name | Purpose | Description |
|-------------|---------|-------------|
| `main` | Production | This is the stable branch that represents what's currently in production. It should be deployable at any time. Merges from `staging` occur here, triggering rebases. |
| `staging` | Integration | This branch serves as an integration branch for features and fixes. It's where changes from `development` are merged and tested together before moving to `main`. |
| `development` | Development Integration | This branch is where features and bug fixes are merged together before they are tested in `staging`. |
| `feature` | Feature Development | These branches are for developing individual features. Once a feature is complete, it's merged into `development`. |
| `bugFix` | Bug Fixes | This branch is for fixing bugs. After the bug is fixed, changes are merged back into `development`. |


## Submodule git commands

| Command | Description | Example |
|---------|-------------|---------|
| `git submodule add [repository] [path]` | Add a new submodule to the current repository. | `git submodule add https://github.com/user/repo.git libs/repo` |
| `git submodule init` | Initialize the submodules recorded in the index. | `git submodule init` |
| `git submodule update` | Fetch the submodule and check out the commit recorded in the superproject. | `git submodule update` |
| `git submodule update --init` | Initialize and update the submodule in one command. | `git submodule update --init` |
| `git submodule update --recursive` | Update the submodule and any nested submodules it contains. | `git submodule update --recursive` |
| `git submodule foreach [command]` | Execute a shell command in each checked-out submodule. | `git submodule foreach git pull` |
| `git submodule status` | Show the status of the submodule (commit it points to). | `git submodule status` |
| `git submodule deinit [path]` | Unregister the given submodule. | `git submodule deinit libs/repo` |
| `git submodule sync` | Synchronize submodule's remote URL configuration based on the superproject's configuration. | `git submodule sync` |
| `git submodule absorbgitdirs` | If a submodule has a `.git` directory, move the `.git` directory from a submodule to the superproject's `.git/modules` directory. | `git submodule absorbgitdirs` |
| `git clone --recurse-submodules [repository]` | Clone a repository and all its submodules. | `git clone --recurse-submodules https://github.com/user/superproject.git` |
| `git pull --recurse-submodules` | Pull from the superproject and all its submodules. | `git pull --recurse-submodules` |
| `git push --recurse-submodules=check` | Check if all submodule commits used by the superproject have been pushed. | `git push --recurse-submodules=check` |
| `git push --recurse-submodules=on-demand` | Push all changed submodules (that have been pushed). | `git push --recurse-submodules=on-demand` |

### Add a submodule

```
git submodule add https://github.com/<user or organization>/<repository>.git
                  - ex. https://github.com/JonahsOrg/submodule-b.git

git add .gitmodules <repository>
                    - ex. submodule-b

git commit -m "added <repository> to core"
              - ex. "added submodule-b to core"

git push
```


## Graphs

### Main repository overview

```mermaid

gitGraph
       commit  id: "v0.0.1"

       branch hotfix
       branch release
       branch staging
       branch development
       branch submodule-a


       checkout submodule-a
       commit tag: "initial commit"
       commit
       commit

       checkout development
       merge submodule-a
       commit
       commit

       checkout staging
       merge development
       commit

       checkout release
       merge staging
       commit tag: "pre-release adjustments"
       commit

       checkout main
       merge release id: "v0.1.0" tag: "trigger a build"

       checkout hotfix
       merge main
       commit tag: "urgent fix"
       commit

       checkout main
       merge hotfix tag: "hotfix applied"

       branch submodule-b
       commit tag: "initial commit"
       commit
       commit
       commit
       commit

       checkout development
       merge submodule-b
       commit
       commit

       checkout staging
       merge development
       commit

       checkout release
       merge staging
       commit tag: "pre-release adjustments for submodule-b"
       commit

       checkout main
       merge release id: "v1.0" tag: "trigger a build"


```

### Submodule repository overview

```mermaid

gitGraph
  commit
  branch staging
  branch development
  branch bugFix
  branch feature1

  checkout feature1
  commit
  commit
  commit

  checkout development
  merge feature1

  checkout staging
  merge development

  checkout main
  merge staging tag: "trigger a rebase"

  branch feature2
  checkout feature2
  commit tag: "introduced bug"
  commit

  checkout development
  merge feature2

  checkout staging
  merge development tag: "found a bug"

  checkout main
  branch feature3
  checkout feature3
  commit
  commit

  checkout bugFix
  merge staging
  commit tag: "fixed the bug"

  checkout development
  merge bugFix tag: "merge bug fix"

  checkout feature3
  commit

  checkout development
  merge feature3

  checkout staging
  merge development

  checkout main
  merge staging tag: "trigger a rebase"

```
