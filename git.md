
## Config

```sh
# Show effective config and from which configuration file it comes from
git config --list --show-origin
```

## Credentials

https://git-scm.com/docs/gitcredentials

**Location of credential helpers (`git-credential-*`)** \
Windows : `%GIT_INSTALL_DIR%/mingw64/libexec/git-core`. \
Linux : `/usr/lib/git-core`


*Git credential manager core* project \
[Git Credential Manager Core: Building a universal authentication experience](https://github.blog/2020-07-02-git-credential-manager-core-building-a-universal-authentication-experience/#:~:text=GCM%20Core%20is%20a%20free,cross-host%20support%20in%20mind.) \
[Project Github repo](https://github.com/GitCredentialManager/git-credential-manager)

```sh
# Credential helpers main interface
# https://git-scm.com/docs/git-credential
> git credential fill     # store, retrieval
protocol=https
host=example.org
(blankline)
> git credential approve  # store
> git crendential reject  # erase

# Credential helper (ex. credential-manager-core)
> git credential-manager-core get
> git credential-manager-core store
> git credential-manager-core erase
```

## Repo migration

Procedure : 

```sh
git clone --mirror <url_origin_repo>
cd <repo_directory>
git remote add new-origin <url_target_repo>
git push new-origin --mirror
```
Pick only some ref subset : https://www.atlassian.com/git/tutorials/git-move-repository

**Misc**
`git remote update` resynchronises everything in the mirrored repos, same as `rmdir` and `git clone --mirror` again.

Some ref types can't be pushed in the target repos (eg. read only pull request refs, etc...).
```sh
> git push new-remote --mirror
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 8 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (13/13), 3.26 KiB | 3.26 MiB/s, done.
Total 13 (delta 3), reused 8 (delta 2)
remote: Resolving deltas: 100% (3/3), done.
To github.com:ctrung/mov-new.git
 * [new branch]      feature/new-procedures -> feature/new-procedures
 * [new branch]      main -> main
 * [new branch]      new-remote/feature/new-procedures -> new-remote/feature/new-procedures
 * [new branch]      new-remote/main -> new-remote/main
 * [new tag]         v1.0 -> v1.0
 ! [remote rejected] refs/pull/1/head -> refs/pull/1/head (deny updating a hidden ref)
error: failed to push some refs to 'git@github.com:ctrung/mov-new.git'
```

