# Github Organization Manager

The Github Organization Manager takes the pain out of managing organizations with many repositories by adding a few missing features-

* Global repository settings so changes don't have to be manually applied to every repository.
* Automatic assignment of new issues to projects.
* A global list of labels (name, color, and description) to use for every repository.

Right now this application supports the following repository settings-

* Whether wikis, projects, and issues are enabled,
* What type of merging is allowed,
* A full list of labels, with descriptions and colors,
* Which project and column to assign new issues to.

## Installing

This app can be installed [directly on Github](https://github.com/apps/organization-manager).

## Configuring

The configuration for your entire organization is managed using a single `organizer.yaml` in the `.github` repository in the organization.

This example keeps most of the github defaults, but adds issue autoassignment and some additional labels-

```yaml

allow_squash_merge: true
allow_merge_commit: true
allow_rebase_merge: true

has_issues: true
has_wiki: true
has_projects: true
has_downloads: true


issues:
  project_autoassign:
    organization: true
    name: My Project
    column: New Issues and Tasks


# Remove labels that aren't managed by this file.
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

  # Useful labels

  - name: security
    color: db5cb3

  - name: cleanup
    color: db5cb3

  - name: refactor
    color: db5cb3

  - name: reusability
    color: db5cb3

  - name: reproducibility
    color: db5cb3

  - name: monitoring
    color: db5cb3

  - name: stability
    color: db5cb3

  - name: needs discussion
    color: 'e08155'
```



----
[View Source on Github](https://github.com/gitconsensus/GithubOrganizer)  
[Install App](https://github.com/apps/organization-manager) 
