Role Name
=========

A brief description of the role goes here.

Requirements
------------

### Ubuntu 16.04

B4 starting remove any existing lxd 2.x stuff

```shell
apt remove --purge lxd lxd-client liblxc1 lxcfs
```

install 3.x

via snap - tested and fully supported by Canonical

```shell
snap install lxd
```

Storage network should be identical on LXD clusters?

First node

```shell
lxd init
clustering yes
joining no
password
new zfs pool, loop
not adding a remote storage pool (like ceph)
MASS no
```

lxc cluster commands examples:

```shell
lxc cluster list
lxc cluster show mycluster
```

Second node

ALL LXD DATA WILL BE WIPED FROM THE CONNECTING NODE

```shell
zfs loop
```

chec kcluster

```shell
lxc cluster
```

Third node

```shell
join 
zfs loop
lxc cluster list
lxc launch ubuntu:16.04 c1
lxc config show
```

Creting contianrs on the cluster

instances show up wherever (random cluster member) unless directed to a specific node like thisL

```shell
lxc launch ubuntu:16.04 d3 --target node2
```



overide ip address example:

```shell
lxc config override c2 eth0 ipv4.address 172.17.16.20
lxc config override c2 eth0 nic nictype=bridged parent=br0 ipv4.address 172.17.16.20 name=eth0
```

expanded config

```shell
lxc config show c1 --expanded
```

storage

```shell
lxc storage volumn list
```

Connecting to a remote lxd cluster

```shell
lxc remote add cluster mycluster
password
client certificate stored at server: cluster
```

Set the cluster as a remote the default

```shell
lxc remote set-default cluster
lxc list
```

To change the default back to the local lxd

```shell
lxc remote set-default local
lxc list
```

Note: The TLS certificate record is shared between all of your cluster nodes so you can create a DNS round robin record that is shared between all of your cluster nodes

so

```shell
ssh node1
poweroff
```

then

```shell
lxc cluster list
```

after about 30 seconds the node will be marked as not available and any containers on that node will show as OFFLINE

when the node is brought back up everything should get back to normal and the containers on the first node will be back online

### Remote LXD server

- LXD should already be setup on the remote image server:

  `sudo lxd init`

- In order for Ansible to manage an LXD host remotely the following commands must be run ahead of time:

On the remote LXD host:

```shell
lxc config set core.https_address [::]:8443
lxc config set core.trust_password replace-this-with-a-secure-password
```

On the local LXD host:

```shell
lxc config set core.https_address [::]:8443
lxc remote add lxd4 lxd4.example.com
```

(replace lxd4.example.com with the hostname of your LXD host, 'lxd4'  can be named whatever you want, you'll need to reference it in the  inventory file)

- Tested on an LXD host and Ansible host both using Ubuntu 16.04 LTS (may work with other distros)

## Testing

### Manual

Connect

```shell
molecule --debug login -s lxd
```

tests

```shell
zpool list
zfs list
```

Container creation check

```shell
lxd list
lxc launch ubuntu:18.04 c1
lxc list
```



Role Variables
--------------

A description of the settable variables for this role should go here, including
any variables that are in defaults/main.yml, vars/main.yml, and any variables
that can/should be set via parameters to the role. Any variables that are read
from other roles and/or the global scope (ie. hostvars, group vars, etc.) should
be mentioned here as well.

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in
regards to parameters that may need to be set for other roles, or variables that
are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables
passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: cjsteel.lxd_server, x: 42 }

## Inspiration

* https://github.com/thebinary/ansible-role-lxd/branches
  * https://galaxy.ansible.com/thebinary/lxd
* https://github.com/rroethof/ansible-module-lxd/tree/master/tasks
  * https://galaxy.ansible.com/rroethof/module-lxd
* https://github.com/hispanico/ansible-lxd
* https://github.com/plumelo/ansible-role-lxd
  * https://galaxy.ansible.com/plumelo/lxd
* https://github.com/peopledoc/ansible-boot-lxd
  * https://galaxy.ansible.com/peopledoc/boot-lxd

License
-------

MIT

Author Information
------------------

An optional section for the role authors to include contact information, or a
website (HTML is not allowed).
