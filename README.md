# rsync backups #

A small set of rsync based backup scripts to run on linux servers

Scripts are run on the Backup Server and using rsync and ssh data is
copied from the servers being backed up.

To simplify access to the remote servers it is recommended the entries
be made to the `~/.ssh/config` file defining server address, port,
user and key file to use.

## Configuration




## Files: ##

### `/usr/local/sbin/rsync-backups` ###

Script that is invoked by `/etc/cron.daily` or `crontab` reads configurations from `/etc/rsync-backup/config` and invokes `replicator` with the appropriate `env` variables setup.

### `/usr/local/lib/rsync-backups/replicator` ###

  * Replicates the latest backup directory to a new directory (named
    with the current date and time)
  * scans the `/etc/rsync-backups/conf.d/` directory for config files
    and runs the worker `sync` scripts that do the actual backup in the
    background until a time limit is reached.

Expects the `env` variable CONFIG_DIR to point to the configuration DIR that
holds the "`conf.d`".

#### Usage

`replicator -d working_directory [ -t time_to_live ]`

  `-d working_directory` - directory (or "Backup Store") where it will
                           look for  work to do

  `-t time_to_live` - optional, for how long, in seconds, the process
                      will live

#### Exit codes

    12 - Unable to find BACKUP_STORE
    13 - Unable to find "latest" inside BACKUP_STORE

### `/usr/local/lib/rsync-backups/sync` ###

Worker script that does the actual backup of a server. Reads it's
configuration from the specified directory.

#### Usage

`sync -c CONFIG_DIR -d BACKUP_DIR`

  `-c CONFIG_DIR` - configuration directory for this server, for example
                   `/etc/rsync-backups/conf.d/CMX/`

  `-d BACKUP_DIR` - path to the directory that will hold the backup, the
                    `sync` script will try to update data that already
                    exists there or create it

### `bkDatabase` ###

Backs up a MySQL database, if this file is present it will be
invoked by the `sync` script. File must be edited to enter server,
database username and database password.

## `functions` ##

File to be placed on `/usr/local/etc/rsyncBackups` or on the backup directory,
together with `replicator`.

This file contains several bash functions used by the other scripts.

## Seeding ##

@TODO THIS SECTION IS OUTDATED

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
