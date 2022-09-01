
## Config

```sh
# Show effective config and from which configuration file it comes from
git config --list --show-origin
```

## Credentials

https://git-scm.com/docs/gitcredentials

In Windows, credential helpers are located in `%GIT_INSTALL_DIR%/mingw64/libexec/git-core/`. \
Binary name is normalized : `git-credential-*.exe`.

*Git credential manager core* project \
[Git Credential Manager Core: Building a universal authentication experience](https://github.blog/2020-07-02-git-credential-manager-core-building-a-universal-authentication-experience/#:~:text=GCM%20Core%20is%20a%20free,cross-host%20support%20in%20mind.) \
[Project Github repo](https://github.com/GitCredentialManager/git-credential-manager)

```sh
# Credential helpers main interface
# https://git-scm.com/docs/git-credential
git credential fill     # store, retrieval
git credential approve  # store
git crendential reject  # erase

# Credential helper (ex. credential-manager-core)
git credential-manager-core get
git credential-manager-core store
git credential-manager-core erase
```
