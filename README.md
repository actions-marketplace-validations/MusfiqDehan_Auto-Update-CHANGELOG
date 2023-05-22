# Auto Update CHANGELOG
A GitHub Action that will make your CHANGELOG updating easier and faster.

## What It Can Do

- Update your CHANGELOG file on every push with previous commit messages. 
- It will trigger if you follow a particular commit message pattern

## Usage

### Workflow

- Create a CHANGELOG.md file in your root directory. If you don't create it will be created automatically.
- To get started, create a workflow `.yml` file in your `.github/workflows` directory. 
- You can give a name like `changelog.yml` or `update-changelog.yml`, so that anyone can understand what your action will do. 
- There is an [example workflow](##example) below. For more information, take a look at the GitHub Help Documentation for [GitHub Actions](https://github.com/features/actions)

## Example

```yml
name: Update Changelog

on:
    push:
        branches:
            - main

jobs:
    update-changelog:
        runs-on: ubuntu-latest

        steps:
            - name: Checkout code
              uses: actions/checkout@v3
              with:
                  fetch-depth: 0

            - name: Get the current version
              id: current-version
              run: echo "version=$(python setup.py --version)" >> $GITHUB_ENV

            - name: Check if the commit message matches the release pattern
              run: |
                  if [[ $(git log -1 --pretty=%B) != "Update changelog for v${{ env.version }}" ]]; then
                    echo "Commit message does not match the release pattern."
                    exit 1
                  fi

            - name: Update changelog
              if: contains(github.event.head_commit.message, 'Update changelog for v${{ env.version }}')
              run: |
                  PREVIOUS_VERSION=$(git tag -l --sort=-creatordate | head -n 1)
                  CHANGES=$(git log --pretty="- %s" $PREVIOUS_VERSION..)
                  echo "# v${{ env.version }}" >> CHANGELOG.md
                  echo "## Changes" >> CHANGELOG.md
                  echo "$CHANGES" >> CHANGELOG.md
                  git config user.email "musfiqur.rahaman@northsouth.edu"
                  git config user.name "Md. Musfiqur Rahaman"
                  git commit -am "Update CHANGELOG.md with latest version by GitHub Action"
                  git push

```

## What Changes You Can Make Here
- `branch` : You can change your branch
```yml
on:
    push:
        branches:
            - main
```
- `commit message patterm`: You can change your commit message pattern
```yml
- name: Check if the commit message matches the release pattern
  run: |
      if [[ $(git log -1 --pretty=%B) != "Update changelog for v${{ env.version }}" ]]; then
        echo "Commit message does not match the release pattern."
        exit 1
      fi
```

## Other Dependency Workflows/Actions
This workflow is dependent on some other workflows. Such as:
- [Auto Release](https://github.com/CupOfTea696/gh-action-auto-release)

## My Other Useful Action
- Auto Update PyPI Release
- Auto Update GitHub Packages Release

