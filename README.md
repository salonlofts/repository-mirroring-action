# repository-mirroring-action

[![Test](https://github.com/pixta-dev/repository-mirroring-action/actions/workflows/test.yml/badge.svg)](https://github.com/pixta-dev/repository-mirroring-action/actions/workflows/test.yml)

A GitHub Action for mirroring a repository to another repository on GitHub, GitLab, BitBucket, AWS CodeCommit, etc.

This will copy all commits, branches and tags.

>⚠️ Note that the other settings will not be copied. Please check which branch is 'default branch' after the first mirroring.

## Usage

Customize following example workflow (namely replace `<username>/<target_repository_name>` with the right information) and save as `.github/workflows/main.yml` on your source repository.

To find out how to create and add the `GITLAB_SSH_PRIVATE_KEY`, follow the steps below:
1. [How to generate an SSH key pair](https://docs.gitlab.com/ee/ssh/#generate-an-ssh-key-pair). Recommended encryption would be _at least_ `2048-bit RSA`.
2. Add the _public_ key to [your gitlab account](https://gitlab.com/-/profile/keys)
3. Add the _private_ key as a secret to your workflow. More information on [creating and using secrets](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets).

Best practices for versioning a forked repo
https://gofore.com/en/best-practices-for-forking-a-git-repo/#BestPracticesofforkinggitrepositoryandcontinuingdevelopment-Howyoushoulddoversioning?

```yaml
name: Mirroring

on: [push, delete]

jobs:
  to_hairdater:
    runs-on: ubuntu-latest
    steps:                                              # <-- must use actions/checkout before mirroring
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - uses: salonlofts/repository-mirroring-action@v1.0.2+salonlofts.1
        with:
          target_repo_url:
            code.hairdater.com:salonlofts-com.git
          ssh_private_key:                              # <-- use 'secrets' to pass credential information.
            ${{ secrets.HAIRDATER_SSH_PRIVATE_KEY }}
          ssh_username:
            ubuntu

  to_codecommit:                                        # <-- different jobs are executed in parallel.
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - uses: salonlofts/repository-mirroring-action@v1.0.2+salonlofts.1
        with:
          target_repo_url:
            ssh://git-codecommit.us-east-1.amazonaws.com/v1/repos/salonlofts-com
          ssh_private_key:
            ${{ secrets.CODECOMMIT_SSH_PRIVATE_KEY }}
          ssh_username:                                 # <-- (for codecommit) you need to specify ssh-key-id as ssh username.
            ${{ secrets.CODECOMMIT_SSH_PRIVATE_KEY_ID }}
```
