Ansible Playbook for PG-REX
===========================

Ansible playbook that set up a PostgreSQL cluster with Pacemaker based on the PG-REX standard.

Requirements
------------

The playbook is tested with the following software versions:

* VirtualBox 7.1.x
* Vagrant 2.4.x
* Vagrant box
  * [rockylinux/9](https://app.vagrantup.com/rockylinux/boxes/9)
* Ansible 2.10.x
* PG-REX 16
  * PostgreSQL 16.x
  * Pacemaker 2.1.x
  * Pacemaker Extra Tools ([pm\_extra\_tools](https://github.com/linux-ha-japan/pm_extra_tools)) 1.6
  * PG-REX Operation Tools ([PG-REX-tools](https://github.com/ossc-db/PG-REX-tools)) 16.0
* VirtualBMC VirtualBox-ported edition ([virtualbmc-vbox](https://github.com/kskmori/virtualbmc-vbox)) devel2.0

Usage
-----

To run the playbook:

```ShellSession
$ git clone https://github.com/tom-sato/ansible-pg_rex.git
$ cd ansible-pg_rex
$ vagrant up --provision
(snip)
PLAY RECAP *********************************************************************
node-1                     : ok=61   changed=46   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node-2                     : ok=37   changed=27   unreachable=0    failed=0    skipped=16   rescued=0    ignored=0

$ ansible-playbook virtualbmc-start.yml
Using /home/tom-sato/work/ansible-pg_rex/ansible.cfg as config file
become password: (Enter your password)
(snip)
PLAY RECAP *********************************************************************
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

$ vagrant ssh node-1
$ LANG=C sudo /usr/local/bin/pg-rex_primary_start pm_pcsgen_env.xml
(snip)
Primary has started on the node (node-1).
$ exit
$ vagrant ssh node-2
$ LANG=C sudo /usr/local/bin/pg-rex_standby_start
(snip)
4.3 Checking if it is ready to run pg_basebackup
...[OK]

Following methods are available to start this node as standby
b) Start from a new base backup
q) quit
Make a choice among the options (b/q) b (Type "b" to continue)
(snip)
Standby has started on the node (node-2).
$ sudo pcs status
Cluster name: pgrex_cluster
Cluster Summary:
  * Stack: corosync (Pacemaker is running)
  * Current DC: node-1 (version 2.1.8-3.el9-3980678f0) - partition with quorum
  * Last updated: Sun Feb 23 00:41:41 2025 on node-2
  * Last change:  Sun Feb 23 00:41:02 2025 by root via root on node-1
  * 2 nodes configured
  * 11 resource instances configured

Node List:
  * Online: [ node-1 node-2 ]

Full List of Resources:
  * Clone Set: pgsql-clone [pgsql] (promotable):
    * Promoted: [ node-1 ]
    * Unpromoted: [ node-2 ]
  * Resource Group: primary-group:
    * ipaddr-primary    (ocf:heartbeat:IPaddr2):         Started node-1
    * ipaddr-replication        (ocf:heartbeat:IPaddr2):         Started node-1
  * ipaddr-standby      (ocf:heartbeat:IPaddr2):         Started node-2
  * Clone Set: ping-clone [ping]:
    * Started: [ node-1 node-2 ]
  * Clone Set: storage-mon-clone [storage-mon]:
    * Started: [ node-1 node-2 ]
  * fence1-ipmilan      (stonith:fence_ipmilan):         Started node-2
  * fence2-ipmilan      (stonith:fence_ipmilan):         Started node-1

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```

License
-------

BSD

Author Information
------------------

Tomoaki Sato
