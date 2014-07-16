# Ansible cookbooks for hecuba

## SETUP

Copy `hecuba.example` to `hecuba`. This file will hold the inventory of
your running machines.

## SEMI MANUAL PROCEDURE

To add a Cassandra Node:

* launch new instance with:
  * ami ID: ami-16ff9626
  * User Data:
`--clustername kixi --totalnodes 3 --version enterprise --username ***** --password *****`
  * tag name: 'kixi-new'
* stop dse: `sudo service dse stop`
* upgrade DSE: `sudo apt-get install dse-full`
* edit/update configuration:
 * /etc/dse/dse.yaml (snitch)
 * /etc/dse/cassandra.yaml
   * `num_tokens: 256`
   * comment out `initial_token`
   * set `seeds`
   * set `auto_bootstrap: true`
   * set `rpc_address`
* move the old data out of the way:
`sudo mkdir /raid0/cassandra.bak; sudo mv /raid0/cassandra/* /raid0/cassandra.bak`
* recreate the log folder:
`sudo mkdir /raid0/cassandra/logs`
`sudo chown cassandra.cassandra /raid0/cassandra/logs`
* restart dse: `sudo service dse start`
* fix /var/lib/datastax-agent/conf/address.yaml with address of opscenter
* run ansible to install nginx
* set hostname: `hostname kixi-dse*` end edit /etc/hostname
* add team ssh keys to the ~/.ssh/authorized_keys

To add an app node:

* launch a new instance with:
  * ami ID: ami-3fcab50f
* add reference to new instance to `kixi` file
* optionally create a file in `host_vars` where to define host specific
  vars (hostname and fqdn are good examples)
* run ansible:
`ansible-playbook -i kixi -vvvv kixi.yml --skip-tags "provisioning"`
* add team ssh keys to the ~/.ssh/authorized_keys
* copy .hecuba.edn to the server

## NOTES
* You will need `boto` installed for this to work:

```
  $ sudo pip install boto
```

