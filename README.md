# rsync backups #

A small set of rsync based backup scripts to run on linux servers

Scripts are run on the Backup Server and using rsync and ssh data is
copied from the servers being backed up.

To simplify access to the remote servers it is recommended the entries
be made to the `~/.ssh/config` file defining server address, port,
user and key file to use.

## Docker

A docker-compose file is provided mainly for testing

## Files: ##

List of files in project.

Files will be copied to ``/usr/local/``

```
  /etc
    /rsync-backups
      conf           (global configurations)
  /lib
    /rsync-backups
      bkDatabase     (worker file to backup databases)
      functions      (functions used by other processes)
      replicator     (worker file that replicates previous backup structure)
      sync           (worker file to sync files to local backup)
  /misc              (miscellaneous to be copied to several places)
    dailyBackup      (invoker, to be placed in /etc/cron.daily)
    install.sh       (installer)
  /sbin
    rsync-backups    (starter, called by cron, reads confs and starts other processes)
  docker-compose.yml (docker file to setup testing environment)
  LICENSE            (the LICENSE)
  README.md          (this file)
```

### `dailyBackup` ###

File to be placed on `/etc/cron.daily` or another place like `/usr/local/sbin/`
and invoked by the root's crontab.

It checks the availability of the backup directory and starts `replicator`.

### `replicator` ###

Replicates the latest backup directory to a new directory (named with the
current date and time) and scans the sub directories for executable files
named `sync` and runs them in the background until a time limit is reached.

#### Usage

`replicator -d working_directory [ -t time_to_live ]`

  `-d working_directory` - directory (or "Backup Store") where it will look for
                           work to do

  `-t time_to_live` - optional, for how long, in seconds, the process will live

#### Exit codes

    12 - Unable to find BACKUP_STORE
    13 - Unable to find "latest" inside BACKUP_STORE

### `sync` ###

Does the backup of a single server to the `data` directory this
script can be highly modified to adapt to the server being backup.  

### `bkDatabase` ###

Backs up a MySQL database, if this file is present it will be
invoked by the `sync` script. File must be edited to enter server,
database username and database password.

## `functions` ##

File to be placed on `/usr/local/etc/rsyncBackups` or on the backup directory,
together with `replicator`.

This file contains several bash functions used by the other scripts.

## Seeding ##

* Create a directory to hold the backups

* Copy the `replicator` script to this directory

* Create a "seeder" directory and symlink named "latest" pointing
  to it

    `mkdir xpto; ln -s xpto latest`

* Inside the `latest` directory create a subdirectory for each of the
  servers that you want to backup

* Copy the `sync` file to servers directory and adapt it to that
  server

* If the server has a MySQL database copy also de `bkDatabase` file
  and adapt it

## Directory structure ##
```
 |-- replicator*
 |-- DATE1 (backup directory 1)
 |   |-- ServerA
 |   |   |-- sync*
 |   |   |-- excludes
 |   |   |-- bkDatabase
 |   |   |-- ServerA_mysqldump.sql.bz2
 |   |   |-- data
 |   |   |   |-- backup-directory-from-server-A-one
 |   |   |   |-- backup-directory-from-server-A-two
 |   |   |   |-- backup-directory-from-server-A-three
 |   |-- ServerB
 |   |   |-- sync*
 |   |   |-- excludes
 |   |   |-- data
 |   |   |   |-- backup-directory-from-server-A-one
 |   |   |   |-- backup-directory-from-server-A-two
 |   |   |   |-- backup-directory-from-server-A-three
 |-- DATE2 (backup directory 2)
 |   |-- ServerA
 |   |   |-- sync*
 |   |   |-- excludes
 |   |   |-- data
 |   |   |   |-- backup-directory-from-server-A-one
 |   |   |   |-- backup-directory-from-server-A-two
 |   |   |   |-- backup-directory-from-server-A-three
 |   |-- ServerB
 |   |   |-- sync*
 |   |   |-- excludes
 |   |   |-- data
 |   |   |   |-- backup-directory-from-server-A-one
 |   |   |   |-- backup-directory-from-server-A-two
 |   |   |   |-- backup-directory-from-server-A-three
 |-- latest (symlink to DATE2)
```
