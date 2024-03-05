## A voir

[So You Think You Know Git - FOSDEM 2024](https://www.youtube.com/watch?v=aolI_Rz0ZqY)

## Aliases

```sh
# add in $HOME/.gitconfig

[alias]
        ## working on commits
        # commit to previous commit
        caa = commit -a --amend -C HEAD
        ci = commit
        co = checkout
        cp = cherry-pick
        dc = diff --cached
        fix = caa
        lg = log --graph --abbrev-commit --decorate --format=format:'%C(yellow)%h%C(reset) %C(normal)%s%C(reset) %C(dim white)%an%C(reset) %C(cyan)(%ar)%C(reset) %C(auto)%d%C(reset)'
        lgp = log --graph --abbrev-commit --decorate --format=format:'%C(yellow)%h%C(reset) %C(normal)%s%C(reset) %C(dim white)%an%C(reset) %C(cyan)(%ar)%C(reset) %C(auto)%d%C(reset)' --first-parent
        rb = rebase
        rba = rebase --abort
        rbi = rebase -i
        rbc = rebase --continue
        rbs = rebase --skip

        ## working on branches
        br = branch
        bn = "!git rev-parse --abbrev-ref HEAD"         # branche name
        clean-merged = !git branch --merged \
                        | grep -v \"\\*\" \
                        | xargs -n 1 git branch -d
        publish = "!git push -u origin $(git bn)"       # set upstream and push
        ## alternative to pull.rebase = true
        # pullr = pull --rebase
        pushf = push --force-with-lease
        sync = rebase origin/develop
        unpublish = "!git push origin :$(git bn)"       # delete remote
        whatsnew = "!git diff origin/$(git bn)...HEAD"
        whatscoming = "!git diff HEAD...origin/$(git bn)"

        # exemple d'alias avec paramètres
		#foo = "!f() { echo $3 $2 $1; }; f"

        # misc
        rz = reset --hard
        rza = !git reset --hard HEAD && git clean -df
        st = status
        eol = ls-files . --eol

        # https://stackoverflow.com/questions/1657017/how-to-squash-all-git-commits-into-one
        # usage : git squash-all -m "a brand new start"
        squash-all = "!f(){ git reset $(git commit-tree HEAD^{tree} \"$@\");};f"

```

## Change remote

``` sh
git remote set-url ORIGIN URL
git checkout BRANCH; git fetch; git reset --hard ORIGIN/BRANCH # update branch state
```

## Config

https://stackoverflow.com/questions/32538639/cannot-find-gitconfig-file

```sh
# Show effective config and from which configuration file it comes from
git config --list --show-origin
git config --list --show-origin --show-scope

git config --show-origin --get user.email
```

Une configuration par namespace. 
Attention : Le chemin défini par gitdir doit comporter un slash à la fin !
```
# windows
[includeIf "gitdir:C:/workspaces/github/"]
    path = C:/workspaces/github/.gitconfig

# linux
[includeIf "gitdir:~/projects/oss/"]
  path = ~/projects/oss/.gitconfig

# vérifier que la surcharge est effective doit se faire au sein d'un repo git 
git config --show-origin --get user.email
```

## Credentials

https://git-scm.com/docs/gitcredentials

Credential are managed through a unique interface called git-credential :
https://git-scm.com/docs/git-credential

```sh
> git credential fill     # retrieval
protocol=https
host=example.org
(blankline)
> git credential approve  # store
> git crendential reject  # erase
```

Internally, a git credential helper is invoked : 
https://git-scm.com/doc/credential-helpers

```sh
# set a credential helper
git config --global credential.helper store|core|cache|manager-core
```

**Location of credential helpers (`git-credential-*`)** \
Windows : `%GIT_INSTALL_DIR%/mingw64/libexec/git-core`. \
Linux : `/usr/lib/git-core`


**A new helper that unfiy the experience on all platform ! : Git credential manager (GCM)** 

Git Credential Manager Core: Building a universal authentication experience \
https://github.blog/2020-07-02-git-credential-manager-core-building-a-universal-authentication-experience/#:~:text=GCM%20Core%20is%20a%20free,cross-host%20support%20in%20mind. 

Github repo \
https://github.com/GitCredentialManager/git-credential-manager

```sh
# Credential helper (ex. credential-manager-core)
> git credential-manager-core get
> git credential-manager-core store
> git credential-manager-core erase
```

Debug GCM :
```sh
export GCM_TRACE=1
export GCM_TRACE_SECRETS=1
```

**Windows specifics**
**>>** Additional configuration in `$GIT_INSTALL_DIR/etc/gitconfig` (only for portable Git installation)  
```sh
[credential]
	helper = manager    # or manager-core for GCM before 2.0.x
[credential "https://dev.azure.com"]
	useHttpPath = true
```
Read https://github.com/git-ecosystem/git-credential-manager/blob/main/docs/rename.md 
for details on helper renaming from `manager-core` to `manager` in GCM v2.0.x.

Credentials are stored in windows credential manager \
https://support.microsoft.com/en-us/windows/accessing-credential-manager-1b5c916a-6a16-889f-8581-fc16e8165ac0 \
https://support.microsoft.com/fr-fr/windows/acc%C3%A8s-au-gestionnaire-d-informations-d-identification-1b5c916a-6a16-889f-8581-fc16e8165ac0

**Linux specifics**
**>>** Installation of Git credential manager (GCM) on Linux

Git does not bundle GCM by default (always ?). Manual install procedure :

Tarball releases : https://github.com/git-ecosystem/git-credential-manager/releases
```sh
# https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/install.md
tar -xvf <path-to-tarball> -C /usr/local/bin   # or -C $HOME/bin 
git-credential-manager configure               # update credential.helper property in gitconfig

# set a cred store for GCM
# see prerequisite https://github.com/git-ecosystem/git-credential-manager/blob/release/docs/credstores.md
git config --global credential.credentialStore secretservice
```

NB : GCM does not select a credential store on Linux by default. If you try to connect to a remote, you will get the following message : 
```fatal: No credential store has been selected.

Set the GCM_CREDENTIAL_STORE environment variable or the credential.credentialStore Git configuration setting to one of the following options:

  secretservice : freedesktop.org Secret Service (requires graphical interface)
  gpg           : GNU `pass` compatible credential storage (requires GPG and `pass`)
  cache         : Git's in-memory credential cache
  plaintext     : store credentials in plain-text files (UNSECURE)

See https://aka.ms/gcm/credstores for more information.
```

## Debugger Git
```sh
export GIT_TRACE_PACKET=1
export GIT_TRACE=1
export GIT_CURL_VERBOSE=1

export GCM_TRACE=1
export GCM_TRACE_SECRETS=1
```

## Disable SSL verify

```sh
# via env var
export GIT_SSL_NO_VERIFY=1
# or via config
git config <--gloabl> http.sslVerify false
```

## Line endings (LF, CRLF)

Great post explaining everything : [CRLF vs. LF: Normalizing Line Endings in Git](https://www.aleksandrhovhannisyan.com/blog/crlf-vs-lf-normalizing-line-endings-in-git/#:~:text=Whereas%20Windows%20follows%20the%20original,the%20era%20of%20the%20typewriter.)

TL;DR :
```
Git config	        .gitattributes	        Index/Repo	Working Tree
core.autocrlf=true	* text=auto eol=crlf	LF	        CRLF
core.autocrlf=input	* text=auto	        LF	        original (LF or CRLF)
```
PS : autocrlf=input affects only new files with CRLF, not existing ones with CRLF.

**Good practices**

Rely on `.gitattributes` (eg. with `* text=auto`) at the root of the repo to force the same policy among all developers.
To renormalize the whole repo from .gitattributes, run  
```shell
git add --renormalize  .
git status                # check everything's ok
git commit                # commit then
```

To check line ending status : 

```shell
git ls-files --eol       # all repo files
git ls-files FILE --eol  # one file
```
should display : `i/lf   w/crlf  attr/text=auto   file.txt` \
**i** means index/repo, **w** means working tree
 
Tips seen on https://stackoverflow.com/questions/55370497/when-git-core-autocrlf-is-set-to-input-what-do-we-really-have. \
To view the file as persisted inside Git index/repo, use the following : 
```shell
git cat-file -p <GIT-REF>:<FILE>
```

Tips seen on https://stackoverflow.com/questions/35801024/how-to-see-what-type-of-line-endings-are-already-in-git-repository
```shell
git show ac8b4a7:bin/script.ksh | file -
/dev/stdin: a /usr/bin/ksh script, UTF-8 Unicode text executable

git show HEAD:bin/script.ksh | file -
/dev/stdin: a /usr/bin/ksh script, UTF-8 Unicode text executable, with CRLF line terminators
```


## Reset author and committer of previous commit

https://www.git-tower.com/learn/git/faq/change-author-name-email \
https://stackoverflow.com/questions/750172/how-do-i-change-the-author-and-committer-name-email-for-multiple-commits

```sh
# update name and email (optional)
git config --global user.name "New Author Name"
git config --global user.email "<email@address.example>"

git commit --amend --no-edit --reset-author                                # last commit

git rebase -r <some commit before all of your bad commits> \
    --exec 'git commit --amend --no-edit --reset-author'                   # partial project history

git rebase -r --root --exec "git commit --amend --no-edit --reset-author"  # entire project history
```

[-r](https://git-scm.com/docs/git-rebase#Documentation/git-rebase.txt--r) : preserve merge commits


## Repo migration (SVN to Git)

Procédure issue du livre Pro Git 2ème édition (version 2.1.411, 2023-09-26)
> paragraphes p.367 et p.399

Autres  : https://learn.microsoft.com/en-us/azure/devops/repos/git/perform-migration-from-svn-to-git?view=azure-devops

```sh
# Importer le repo SVN distant vers un repo Git local
$ git svn clone http://HOST/usvn/svn/REPO -s --username admin

# fichiers à ignorer
$ git svn show-ignore > .git/info/exclude
$ git svn create-ignore

# Fichier users.txt pour mieux mapper les utilisateurs lors du git svn clone.
# Format :
# schacon = Scott Chacon <schacon@geemail.com>
# selse = Someo Nelse <selse@geemail.com>
# La commande ci dessous permet de créer le contenu du users.txt
$ svn log --xml --quiet | grep author | sort -u | perl -pe 's/.*>(.*?)<.*/$1 = /'
# Importer le repo SVN distant avec le fichier des users
$ git svn clone http://HOST/usvn/svn/REPO/ --authors-file=users.txt --no-metadata --prefix "" -s WORKING_TREE_DIR

# Move remote tags to be proper local tags
$ for t in $(git for-each-ref --format='%(refname:short)' refs/remotes/tags); do git tag ${t/tags\//} $t && git branch -D -r $t; done

# Move remote branches to be proper local branches
$ for b in $(git for-each-ref --format='%(refname:short)' refs/remotes); do git branch $b refs/remotes/$b && git branch -D -r $b; done

$ git branch -d trunk

$ git checkout -b develop master  # renommage de master en l'équivalent de la branche principale suivant les conventions de nommage en vigueur 
$ git branch -d master

$ git remote add origin URL
$ git push origin --all
$ git push origin --tags
```

### Non standard layout

Example : git svn clone a subfolder

https://stackoverflow.com/questions/17865386/git-svn-clone-of-a-single-directory-of-svn-repository

```shell
git svn clone SVN_URL \
              --authors-file=USERS.txt \
              --no-metadata \
              --prefix "" \
              --trunk="trunk/$subdir"
              --branches="branches/$subdir*" \
              --tags="tags/$subdir*" \
              --r "$revs_range" \
              GIT_REPO 
```

## Rebase from first commit

`git rebase -i --root` 


## Repo relocation

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

## Silencieux

https://stackoverflow.com/questions/8943693/can-git-operate-in-silent-mode

```shell
git ... --quiet           # moins verbeux (--quiet doit être en dernier)
git ... -q

git ... >/dev/null 2>&1   # complètement silencieux
```

## Squash all commits

See https://stackoverflow.com/questions/25356810/git-how-to-squash-all-commits-on-branch

```sh
git switch yourBranch
git reset --soft $(git merge-base master HEAD)
git commit -m "one commit on yourBranch"
```

## Submodules

Submodule are like symbolic link to another Git repo inside a parent Git repo.
The parent repo doesn't version the submodules files, just the metadata of the link to the nested repo.  

```shell

# Adding a submodule
$ mkdir vendor; cd vendor
$ git sumodule add URL                  # add a submodule inside our own repo in a eg. vendor sub folder
$ git commit ...                        # persist submodule config into the repo 

# Cloning does not init the submodules by default, you have to run a command...
$ git submodule update --init --recursive 

# To clone and init the submodules, there's an option
$ git clone --recurse-submodules URL 
```

## Untrusted certificate (TLS)

Eg. corporate proxy rewritten certificates.

```sh
export GIT_CURL_VERBOSE=1

git fetch
* Couldn't find host dev.azure.com in the .netrc file; using defaults
* About to connect() to proxy PROXY port 80 (#0)
*   Trying 172.25.52.104...
* Connected to PROXY (172.25.52.104) port 80 (#0)
* Establish HTTP proxy tunnel to dev.azure.com:443
> CONNECT dev.azure.com:443 HTTP/1.1
Host: dev.azure.com:443
User-Agent: git/1.8.3.1
Proxy-Connection: Keep-Alive
Pragma: no-cache

 

< HTTP/1.1 200 Connection established
< Date: Tue, 06 Jun 2023 09:56:10 GMT
< Proxy-Connection: Keep-Alive
< Via: 1.1 localhost.localdomain
<
* Proxy replied OK to CONNECT request
* Initializing NSS with certpath: sql:/etc/pki/nssdb
*   CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
* Server certificate:
*       subject: CN=dev.azure.com,O=Microsoft Corporation,L=Redmond,ST=WA,C=US
*       start date: Feb 21 17:47:20 2023 GMT
*       expire date: Feb 16 17:47:20 2024 GMT
*       common name: dev.azure.com
*       issuer: CN=proxy,OU=MY COMPANY,O=MY ORGANIZATION,L=Paris,ST=France,C=FR
* NSS error -8172 (SEC_ERROR_UNTRUSTED_ISSUER)
* Peer's certificate issuer has been marked as not trusted by the user.
* Closing connection 0
fatal: unable to access 'https://dev.azure.com/ORG/PROJECT/_git/REPO/': Peer's certificate issuer has been marked as not trusted by the user.
```

## Update Git to last version (linux)

```sh
# --- Linux Mint
sudo add-apt-repository ppa:git-core/ppa -y  # add Ubuntu Git Maintainers PPA – Stable Branch
sudo apt update                              # refresh apt cache
apt-cache policy git                         # check available versions
sudo apt install git -y                      # install latest version
``` 
