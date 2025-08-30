# Links

[Jujutsu docs website](https://jj-vcs.github.io/jj/latest/)

[Jujutsu (jj) - quand Google réinvente Git en mode ninja](https://korben.info/jj-jujutsu-version-control-alternative.html)

# Commands

From : [Jujutsu (jj) Tutorial - Git-compatible VCS](https://www.youtube.com/watch?v=MR6KSB6I_60) by Matthew Sanabria

jj config file at : `~/.config/jj/config.toml`

```sh
jj git init --colocate

jj bookmark track main@origin
jj bookmark create <name> -r <revision>   # first letter jj change id supported
jj bookmark create foobar -r @-           # from parent commit
jj bookmark delete foobar

jj           # show history

jj describe  # change message
jj describe -r u                          # amend

jj status

jj diff
jj diff --from main
jj diff --from readme@sudomateo           # readme = bookmark, sudomateo = remote
jj diff --to   main

jj commit
jj split                                  # commit a subset 

jj undo                                   # undo previous op
jj operation                              # show op

jj git tag                                # list tags 

jj new bar                                # creates a branch from

jj rebase -b foo -d bar
jj edit                                   # conflict resolution

jj squash --from foo --into bar

jj git push --bookmark foobar --allow-new
jj git push --bookmark foobar

jj abandon o   # deletes a commit

jj git remote add origin git@github.com:/...

jj git fetch
jj git fetch --all-remotes
```
