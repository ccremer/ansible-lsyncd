# ansible-lsyncd

Installs and configures the latest [lsync](https://github.com/axkibe/lsyncd) release.

## Requirements

- Root (sudo) access
- Github and package repo access
- SSH passwordless access between the hosts

## Usage

Example usage:
```yaml
- hosts: webservers
  vars:
    lsyncd_targets:
      - source: /var/www/html
        target: /backup/html
        host: backupserver.localdomain
        desc: Backup of html data # desc is optional, it just adds a comment
  roles:
    - role: ccremer.lsyncd
```

## Role Variables

Advanced usage (with default values):
```yaml
lsyncd_logging_dir: /var/log/lsyncd
lsyncd_logging_status_file: lsyncd.status
lsyncd_logging_file: lsync.log
lsyncd_conf: /etc/lsyncd.conf.lua
lsyncd_targets: []
lsyncd_extra_settings: []
lsyncd_retry: true # the 'insist' option
lsyncd_release: release-2.2.2 # the tar.gz source file on github
lsyncd_update: false # skip installing new version, just update config
lsyncd_default_rsync_args: # default rsync options for all sync targets
  - "archive = true"
lsyncd_cleanup: true # remove some build packages after installation
```

## Example with local mirror

```yaml
- hosts: webservers
  vars:  
    lsyncd_targets:
    - source: /local/whatever
      target: /mnt/tapearchive
      proto: default.rsync
      extra_args:  # this list is within the 'sync' section
        - "excludeFrom = \"/etc/lsyncd/tapearchive.list\""
        - "delay = 15"
      rsync_extra_args: # this list is within the 'rsync' section
        - "_extra = { \"--omit-dir-times\" }"
        - "compress = true"
```
## Gotchas

- This role does not setup the SSH connection between the hosts.
- You may need to increase the inotify watches, in which case you need to define
  `lsyncd_max_user_watches: 524288` (default on debian is 8192, which may be too low).
