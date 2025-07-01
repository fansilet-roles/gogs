gogs-quadlet
=========

Deploy [Gogs](https://gogs.io/) as a [Podman Quadlet](https://gogs.io/) Container

Requirements
------------

* `podman` - version `4.7.2+`  

Role Variables
--------------

* `gogs_quadlet`: (`dict`) - Define the Container image and tag  
* `gogs_quadlet_volumes`: (`list`) A list of volumes and paths to be
  monted on the container.  
* `gogs_quadlet_ports`: (`list`) A list of ports to be published by the gogs
  container  
* `gogs_quadlet_psql_volumes`: (`list`) A list of volumes to be
  mounted on the PostgreSQL container.  
* `gogs_quadlet_psql`: (`dict`) Define the PostgreSQL container image and
  ta to used.  
* `gogs_quadlet_psql_ports`: (`list`) List of ports to be published by
  PostgreSQL container  
* `gogs_quadlet_psql_env`: (`dict`) Environment variables binded to the
  PostgreSQL container. 
* `gogs_pod`: (`dict`) Defines the kube pod metadata

Dependencies
------------

* `container.podman` - version `1.11.0+`

Example Playbook
----------------

* Basic Gogs Deployment:

_deploy gogs and postgresql, postgresql user is the same of `GOGS_USER`_
_by default gogs will be deployed and bind volumes from `/srv/gogs` created on
the host_

```yaml
---
- name: Deploying Gogs
  hosts: server
  gather_facts: false
  vars:
    gogs_quadlet_psql_env:
      TZ: "ETC/UTC'
      POSTGRESQL_PASSWORD: "admin"
      GOGS_PASSWORD: "gogs-admin"
      GOGS_USER: "gogs"
      POSTGRES_DB: "gogs"

  roles:
   - gogs-quadlet
```

_check-out the default variables from [main](defaults/main.yml) to see the full
list of supported values_

Backup and Restore
-------------------

This role installs by default a SystemD timer to backup the Gogs.
The backup runs by default every wed at 3:00 AM.
Backups of the `gogs-bkp` volume are saved in the `/srv/gogs/volbkp/` directory.

The backup is generated using the script [`gogs-bkp.sh`](files/gogs-bkp.sh), 
which is installed in the directory: `/usr/local/bin/gogs-bkp.sh`. 

* Manually Creating a Backup with SystemD:
```bash
systemctl start gogs-bkp
```
_Volume backup will be saved in `/srv/gogs/volbkp/` use 
`systemctl status gogs-bkp` to check the status of the backup job._

* Creating a backup by hand:
_this command will create a zipped backup of all settings and data_
```bash
gogs-bkp.sh backup gogs-gogs # gogs-gogs is the container name where gogs is running
```

* Listing the Backups
_this command will list all backups available in the backup volume_ 
```bash
gogs-bkp.sh list gogs-bkp # gogs-bkp is the container backup volume
```

* Restoring a Backup
_to restore a backup generated with the command `gogs-bkp.sh backup`, use:

```bash
gogs-bkp.sh restore gogs-gogs /backup/my-gogs-backup.zip
```

* Creating a Volume Backup
_The backups created with the `gogs-bkp.sh backup` command are stored in the `gogs-bkp` volume,
this volume is also recommended to be backed up using the `gogs-bkp.sh volbkp` command:_

```bash
gogs-bkp.sh volbkp gogs-bkp /tmp # gogs-bkp is the container backup volume
```
_the above command will create a backup of the `gogs-bkp` volume in the `/tmp` directory.

* Restoring a Backup
_To restore a volume backup generated with the command `gogs-bkp.sh volbkp`, use:

```bash
gogs-bkp.sh volrestore gogs-bkp gogs-bkp-2025-10-01_03-00-00.tar.bz2
```

Developing and Testing
----------------------

This role was developed using [ansible
molecule](https://ansible.readthedocs.io/projects/molecule/).
The use of molecule is optional but recommended.

> **Note**
> Testing this role with Molecule requires creating local secrets for privilege escalation during the `prepare` phase. Please read the [secret management instructions](secrets/README.md) before running `molecule` commands.

- Testing:  
  Unit tests for checking code regression are available in the [`tests` directory](tests/).
  use the `verify` or `test` commands, e.g:

```bash
molecule test
```

while developing use `verify` instead:  

```bash
molecule create
molecule verify
```

License
-------

[GPL-2.0-or-later](https://spdx.org/licenses/GPL-2.0-or-later.html)

Author Information
------------------

@mrbrandao - Igor Brandão
