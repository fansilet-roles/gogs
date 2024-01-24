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


Developing and Testing
----------------------

This role was developed using [ansible
molecule](https://ansible.readthedocs.io/projects/molecule/).
The use of molecule is optional but recommended.  
  
* Testing:  
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
