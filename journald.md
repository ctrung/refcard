## Commands
```sh
# man journald config
man journald.conf

# a unit logs
$ journalctl -u service_name

# follow
$ journalctl -f

# by PID
$ journalctl _PID=123

# time filters
$ journalctl --since 1 hour
$ journalctl --since today

# by boot history (0=current, -1=previous, etc...)
$ journalctl -b

# by priority
$ journalctl -p err

# read old journal (contains @suffix)
$ journalctl --file=/run/log/journal/55937196b8114a19b103a0ce0e859f48/system@b30a4c6ac6874180943d4ed11eed7cd1-00000000008c987b-00061515aba3e80d.journal

```

## Archive old journal entries
https://serverfault.com/questions/1036286/best-way-to-archive-journald-logs-in-a-space-efficient-way
```sh
# depending on storage options
xz /run/log/journal/**/*@*.journal
xz /var/log/journal/**/*@*.journal
```
