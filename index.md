---
layout: page
---

The Github Organization Manager makes it easier to manage Github Organizations. It automatically labels and pushes issues to projects, lets you define standard labels throughout all of your repositories, and lets you define repository settings in a single place that get pushed to all your repos. This way you can be sure that things like branch protection, vulnerability scanning, and team access are set consistently throughout your organization.

* TOC
{:toc}

## Installing

This app can be installed [directly on Github](https://github.com/apps/organization-manager).

Once installed you'll need to create a file named `organizer.yaml` in the `.github` repository in your organizationl. This file will contain all of the settings you want to have applied to your organization.


## Repository Settings

### Configuring Repository Features

Each repository has a variety of Github features, such as projects and wikis, that this project can ensure are enabled or disabled.

```yaml
repositories:
  default:
    features:
      has_issues: true
      has_wiki: false
      has_projects: true
      has_downloads: true
```


### Set Allowed Merge Types

Organizations can enforce specific merge types, preventing people from using the wrong type accidentally.

```yaml
repositories:
  default:
    merges:
      allow_squash_merge: true
      allow_merge_commit: true
      allow_rebase_merge: true
```



### Automatically Label Issues

Issues can have labels applied automatically based off of their repository.

```yaml
repositories:
  default:
    issues:
      auto_label:
        - dev
  production:
    issues:
      auto_label:
        - dev
        - production
  example_repo: production
  another_repo: production
```

It's also possible to set this using [repository settings](#automatically-label-repository-issues).


### Assign Issues to Projects

Issues can get assigned to projects automatically, prevent issues from getting lost in the shuffle if someone forgets to assign one on creation. These projects can be either local repository projects or organization level projects.

```yaml
repositories:
  default:
    issues:
      project_autoassign:
        organization: true
        name: TestProject
        column: To do
```


### Assign Teams and Permissions

Another important use case is adding teams to repositories. This makes it easy to add default teams to all repositories as well as special teams to specific repositories or repository groups.

```yaml
repositories:
  default:
    teams:
      devs: Push # Pull, Push, Admin
      admins: Admin # Pull, Push, Admin
      support: Pull # Pull, Push, Admin
  production:
    teams:
      devs: Pull
      infra: Push
  example_repo: production
  another_repo: production
```

### Set Branch Protection

Branch protection is an important piece of quality control, and can even be part of an organization's compliance program. Branch protection can be set globally, with additional requirements

```yaml
repositories:
  default:
    branches:
      master:
        enforce_admins: false
        required_status_checks:
          require_review: true


  production:
    branches:
      master:
        enforce_admins: true
        required_status_checks:
          require_review: true

      prod:
        enforce_admins: true
        required_status_checks:
          require_review: true
          strict: true

  example_repo: production
  another_repo: production
```


Branch protection has a lot of potential configuration options, including:

* **dismiss_stale_reviews**: will dismiss reviews if they become stale (such as having a new commits).
* **require_code_owner_reviews**: will require the [code owner](https://help.github.com/en/articles/about-code-owners) (if one is set) to perform a review.
* **enforce_admins**: will remove the ability for admins to override the protections to force a merge.
* **required_approving_review_count**: sets a specific number of people required to approve reviews.

In addition the `required_status_checks` has a few options of its own-

* **strict**: requires a pull request to be up to date with the branch before it can be merged.
* **context**: when empty all tests must pass, otherwise it takes an array individual tests (such `ci/circleci` or `coverage/coveralls`) which must pass.
* **require_review**: when set to true at least one review must occur before a pull request can be merged into the branch.

```yaml
repositories:
  default:
    branches:
      master:
        dismiss_stale_reviews: false
        require_code_owner_reviews: false
        enforce_admins: false
        required_approving_review_count: 5
        required_status_checks:
           strict: false
```



### Enforce Dependency Security

A fantastic new feature of Github is that it can scan your repositories for upstream vulnerabilities and even make pull requests to resolve them. This feature is opt in, making it easy to forget. This project lets you set it on the organization level, so you never have to worry about accidentally leaving a repository vulnerable.

```yaml
repositories:
  default:
    dependency_security:
      alerts: true
      automatic_fixes: true
```


## Teams

This function will create teams end keep their members up to date with the list in the configuration file, adding and removing members as needed.

```yaml
teams:
  admins:
    members:
      - tedivm
  devs:
    members:
      - tedivm
      - AliLynne
```

## Labels

### Manage Organization Labels

Keeping labels in sync can be a huge chore when a repository has a large number of repositories. This project keeps labels in sync, optionally cleaning up labels which aren't in the organization file.

```yaml

labels_clean: true

labels:

  # Built in labels.

  - name: bug
    description: "Something isn't working"
    color: d73a4a

  - name: documentation
    description: 'Improvements or additions to documentation'
    color: 0075ca

  - name: duplicate
    description: 'This issue or pull request already exists'
    color: cfd3d7

  - name: good first issue
    description: 'Good for newcomers'
    color: 7057ff

  - name: enhancement
    description: 'New feature or request'
    color: a2eeef

  - name: help wanted
    description: 'Extra attention is needed'
    color: 008672

  - name: invalid
    description: "This doesn't seem right"
    color: e4e669

  - name: question
    description: "Further information is requested"
    color: d876e3

  - name: wontfix
    description: "This will not be worked on"
    color: ffffff


  # Teams/Groups

  - name: infra
    color: db5cb3

  - name: dev
    color: aaffe0

```

### Automatically Label Repository Issues

A common request is to give all issues in a specific repository labels without having to define a per-repository set of configurations. This can be done with the `repos` setting for the label.

```yaml
labels:
  - name: infra
    description: "Infrastructure Team"
    color: d73a4a
    repos:
      - infra-terraform
      - infra-puppet
```

It's also possible to set this using [repository settings](#automatically-label-issues). Both of these settings will work together.


## Full Example

```yaml

teams:
  Admins:
    members:
      - tedivm
  Developers:
    members:
      - tedivm
      - AliLynne
  Website:
    members:
      - tedivm
      - AliLynne



repositories:

  # All Repositories not assigned to other groups.
  default:
    teams_clean: true
    teams:
      Admins: admin
      Developers: push
    issues:
      auto_label:
        - dev
      project_autoassign:
        organization: true
        name: Primary Planning
        column: New Issues and Tasks
    dependency_security:
      alerts: true
      automatic_fixes: true
    merges:
      allow_squash_merge: false
      allow_merge_commit: false
      allow_rebase_merge: true
    features:
      has_issues: true
      has_wiki: false
      has_projects: false
      has_downloads: false

  # Any repository specifically assigned to "production".
  production:
    extends: default
    teams_clean: true
    teams:
      Admins: admin
      Developers: pull
    issues:
      auto_label:
        - dev
        - prod
      project_autoassign:
        organization: true
        name: Primary Planning
        column: New Issues and Tasks
    branches:
      master:
        enforce_admins: true
        required_status_checks:
          strict: true
          require_review: true
  website:
    extends: default
    teams:
      Admins: admin
      Website: pull

  # Individual repository assignments.
  .github: production
  GithubOrganizer: production
  GitConsensusService: production
  GitConsensusCLI: production
  organizer.gitconsensus.com: website
  www.gitconsensus.com: website


labels_clean: true

labels:

  # Built in labels.

  - name: bug
    description: "Something isn't working"
    color: d73a4a

  - name: documentation
    description: 'Improvements or additions to documentation'
    color: 0075ca
    repos:
      - organizer.gitconsensus.com
      - www.gitconsensus.com

  - name: duplicate
    description: 'This issue or pull request already exists'
    color: cfd3d7

  - name: good first issue
    description: 'Good for newcomers'
    color: 7057ff

  - name: enhancement
    description: 'New feature or request'
    color: a2eeef

  - name: help wanted
    description: 'Extra attention is needed'
    color: 008672

  - name: invalid
    description: "This doesn't seem right"
    color: e4e669

  - name: question
    description: "Further information is requested"
    color: d876e3

  - name: wontfix
    description: "This will not be worked on"
    color: ffffff


  # Useful labels

  - name: security
    color: db5cb3

  - name: refactor
    color: db5cb3
    repos:
      - test_rep

  - name: stability
    color: db5cb3

  - name: needs discussion
    color: 'e08155'

```
