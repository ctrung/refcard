## Sources

https://www.thegeekstuff.com/2010/07/logrotate-examples/

## Fichiers

**Bin**     : `/usr/sbin/logrotate` ou `/sbin/logrotate` \
**CRON**    : `/etc/cron.daily/logrotate` \
**Configs** : fichier `/etc/logrotate.conf` et dossier `/etc/logrotate.d/`

## Options

/var/log/messages {
    compress
    rotate  91
    daily
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
    endscript
}

`compress` : compression \
`rotate X` : rotation et conservation de X fichiers \
`daily` : rotation journalière
