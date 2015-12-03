# rsync backups #

A small set of rsync based backup scripts to run on linux servers

Scripts are run on the Backup Server and using rsync and ssh data is 
copied from the servers being backed up.

To simplify access to the remote servers it is recommended the entries
be made to the `~/.ssh/config` file defining server address, port, 
user and key file to use.

Files: 

* `replicator`
> replicates the latest backup directory to a new directory, scans 
> the sub directories for executable files named `sync` and runs them 
> in the background until a time limit is reached

* `sync`
> does the backup of a single server to the `data` directory this 
> script can be highly modified to adapt to the server being backup 

## Seeding ##



...
#!
|-- replicator*
|-- backup-directory-1
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
|-- backup-directory-2
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
...