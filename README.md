# cassandra22x-ansible-backup-restore
## README

### What the heck is this and what is it about?
This ansible backup / restore solution was developed with the following specifics. How to see these specifics includes a newbie how to that I wished existed for me when I was a 100% Linux world newbie... tip of the hat to the newbies who are trying to survive after losing the ease of a Microsoft world. 

Don't feel like a =:-). You know what this means if you are an old MS employee from a long time ago... in a galaxy not so far away. Double Space ate my drive... oh no... should've used Stacker. ¯\_(ツ)_/¯ yeah... I'm that old.

To avoid all of this hassle... Do give Datastax, a commercial dirivative of Cassandra, a try. It seems to support backup/restore out of the box.

### Quick notes about git repo...
- The git repo is organized by the ansible host file system under ansible ... ansible/etc/ansible ... where ansible is the host and /etc/ansible is the target path on the ansible host.
- The git repo is organized by the cassandra noe file system under cassandra ... cassandra/ ... where / is the root of the cassandra node and the cassandra config and data directories are the default /etc/cassandra/conf and /var/lib/cassandra/data.    

## Environment
### Software Versions
- Linux - CentOS Linux release 7.5.1804 (Core) w/ minimal, dev tools, compatibility
  - https://www.centos.org/
- Ansible - ansible 2.7.0
  - https://www.ansible.com/    
  - Newbie How To? ansible --version
- Java - openjdk version "1.8.0_181"
  - https://openjdk.java.net/
- Cassandra - 2.2.13
  - http://cassandra.apache.org/

### other (not part of default CentOS 7 install) repos configured on all systems of the sample environment
- cassandra.repo - http://cassandra.apache.org/doc/4.0/getting_started/installing.html
- epel.repo - https://fedoraproject.org/wiki/EPEL
  
### Ansible and Cassandra Node HW
Nothing special ...  1 CPU, 1GB RAM, 20GB Disk per VM all running on an average developer/ops type workstation with a i7-2600K@3.4GHz CPU, 16GB of RAM, RAID-10 SSDs for host OS and developer/ops data, and SATA spinning rust x2 for all VMs with a deployment like this ... ansible cass2 spinning rust 1 | cass 1 cass 3 spinning rust 2 ... so, you can see the type of imbalance that I expect to see in terms of ansible output. The host OS is good old Windows 7 using Oracle Virtual Box for the VMs. Cassandra configuration is all defaults. The only alertaions to any config file was to configure the clusters's minimal settings that include remote networking.

#### =) begin rant
Windows 7 note... this is probably the last MS OS that I use on a desktop or laptop. Personally, I'm not a fan of my laptop and workstations being turned into phones. ;) Don't get me wrong. I owe MS my livlihood; I've made a 25+ career focused on MS technologies. My MCP number 21695. I've had all kinds of system and development certification from Lotus Notes to Novell CNE to MCSD/MCDBA/MCSE/MCT... I don't ever remember them all. As an early MCT, I'm even old enough to have taught Windows NT 3.1 Advance Server courses. As you can see, I'm that old school and dyed in the wool MS guy who can honestly say that he's been MS-RDBMS guy since SQL Server 4.2 on OS/2. Yep... that's right OS/2. ;) Which is a segway to NoSQL v SQL ... ACID v CAP ... whatever ... The most important thing to accept about Cassandra as a developer is that there is a paradigm shift that you experiece when you "get it." That is you see how important those things in the very beginning of the best practices for development of Cassandra based storage applications are. Those things are data modeling, query planning, logical modeling, physical modeling, up front capacity, performance, scalability, and reliability planning, and accepting the following realities of Cassandra ...
- Follow the book’s recommendations for development process including all artifact creation in the way they show to make them. Don't make something work. Make everything that you possibly can easy on you to make the paradigm shift.
- Test everything in a realistic test environment. You will be developing more "applications" with cassandra-stres before you every write a single line of application code that will be your end development goal.
- Cassandra seems to demand the Full Stack DevOpsliper or Developer Operator. Success seems to hinge on a harmony between the development side and operational side of Cassandra. Cassandra is NOT agile.
- High Maintenacne! Nothing in Cassandra is set it and forget it. With Windows/SQL Server, we’ve been able to set and forget more than we manage. This is the complete opposite for Cassandra. Cutting corners is dangerous to uptime.
There is a place in production for Cassandra. Even though it is seemingly hard, complex, etc., it does fill a specific use case niche and is a blast to work with when production gets the attention and investment that it requires.
#### end rant =)

### The Cassandra Cluster
The cluster from an ad hoc Ansible every node (overkill) POV (point of view)...
- <p><code>[root@ansible ~]# ansible cluster22 -a"nodetool info"</code></p>
- <p><code>[root@ansible ~]# ansible cluster22 -a"nodetool status"</code></p>
- <p><code>[root@ansible ~]# ansible cluster22 -a"nodetool describecluster"</code></p>
- nodetool documentation
  - nodetool - https://docs.datastax.com/en/archived/cassandra/2.1/cassandra/tools/toolsNodetool_r.html
  - easiest to use good enough docs ^
  - http://cassandra.apache.org/doc/4.0/tools/nodetool/nodetool.html
  - official docs ... meh ... ^

#### ansible cluster22 -a"nodetool info" output
```
cass2.deltakappa.com | CHANGED | rc=0 >>
ID                     : fbf6aa22-51af-4b79-867f-77d4524d9efa
Gossip active          : true
Thrift active          : false
Native Transport active: true
Load                   : 113.73 MB
Generation No          : 1539965741
Uptime (seconds)       : 15402
Heap Memory (MB)       : 121.97 / 486.00
Off Heap Memory (MB)   : 0.17
Data Center            : deltakappa
Rack                   : rack1
Exceptions             : 0
Key Cache              : entries 40, size 3.6 KB, capacity 24 MB, 167 hits, 208 requests, 0.803 recent hit rate, 14400 save period in seconds
Row Cache              : entries 0, size 0 bytes, capacity 0 bytes, 0 hits, 0 requests, NaN recent hit rate, 0 save period in seconds
Counter Cache          : entries 2, size 248 bytes, capacity 12 MB, 0 hits, 0 requests, NaN recent hit rate, 7200 save period in seconds
Token                  : (invoke with -T/--tokens to see all 256 tokens)

cass3.deltakappa.com | CHANGED | rc=0 >>
ID                     : 0222aa0b-a9d0-41cb-8fce-0b0b94d17ba8
Gossip active          : true
Thrift active          : false
Native Transport active: true
Load                   : 111.31 MB
Generation No          : 1539965809
Uptime (seconds)       : 15375
Heap Memory (MB)       : 113.43 / 486.00
Off Heap Memory (MB)   : 0.20
Data Center            : deltakappa
Rack                   : rack1
Exceptions             : 0
Key Cache              : entries 49, size 6.84 KB, capacity 24 MB, 163 hits, 225 requests, 0.724 recent hit rate, 14400 save period in seconds
Row Cache              : entries 0, size 0 bytes, capacity 0 bytes, 0 hits, 0 requests, NaN recent hit rate, 0 save period in seconds
Counter Cache          : entries 0, size 0 bytes, capacity 12 MB, 0 hits, 0 requests, NaN recent hit rate, 7200 save period in seconds
Token                  : (invoke with -T/--tokens to see all 256 tokens)

cass1.deltakappa.com | CHANGED | rc=0 >>
ID                     : a6d9f51e-e665-4062-9063-c0f62a5cd7ed
Gossip active          : true
Thrift active          : false
Native Transport active: true
Load                   : 103.14 MB
Generation No          : 1539965669
Uptime (seconds)       : 15504
Heap Memory (MB)       : 258.73 / 486.00
Off Heap Memory (MB)   : 0.17
Data Center            : deltakappa
Rack                   : rack1
Exceptions             : 0
Key Cache              : entries 46, size 4.15 KB, capacity 24 MB, 174 hits, 239 requests, 0.728 recent hit rate, 14400 save period in seconds
Row Cache              : entries 0, size 0 bytes, capacity 0 bytes, 0 hits, 0 requests, NaN recent hit rate, 0 save period in seconds
Counter Cache          : entries 2, size 248 bytes, capacity 12 MB, 0 hits, 0 requests, NaN recent hit rate, 7200 save period in seconds
Token                  : (invoke with -T/--tokens to see all 256 tokens)
```
#### ansible cluster22 -a"nodetool status" output
```
cass2.deltakappa.com | CHANGED | rc=0 >>
Datacenter: deltakappa
======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  10.10.10.51  103.14 MB  256          100.0%            a6d9f51e-e665-4062-9063-c0f62a5cd7ed  rack1
UN  10.10.10.52  113.73 MB  256          100.0%            fbf6aa22-51af-4b79-867f-77d4524d9efa  rack1
UN  10.10.10.53  111.31 MB  256          100.0%            0222aa0b-a9d0-41cb-8fce-0b0b94d17ba8  rack1

cass3.deltakappa.com | CHANGED | rc=0 >>
Datacenter: deltakappa
======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  10.10.10.51  103.14 MB  256          100.0%            a6d9f51e-e665-4062-9063-c0f62a5cd7ed  rack1
UN  10.10.10.52  113.73 MB  256          100.0%            fbf6aa22-51af-4b79-867f-77d4524d9efa  rack1
UN  10.10.10.53  111.31 MB  256          100.0%            0222aa0b-a9d0-41cb-8fce-0b0b94d17ba8  rack1

cass1.deltakappa.com | CHANGED | rc=0 >>
Datacenter: deltakappa
======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address      Load       Tokens       Owns (effective)  Host ID                               Rack
UN  10.10.10.51  103.14 MB  256          100.0%            a6d9f51e-e665-4062-9063-c0f62a5cd7ed  rack1
UN  10.10.10.52  113.73 MB  256          100.0%            fbf6aa22-51af-4b79-867f-77d4524d9efa  rack1
UN  10.10.10.53  111.31 MB  256          100.0%            0222aa0b-a9d0-41cb-8fce-0b0b94d17ba8  rack1
```
#### ansible cluster22 -a"nodetool describecluster" output
```
cass2.deltakappa.com | CHANGED | rc=0 >>
Cluster Information:
        Name: cluster22
        Snitch: org.apache.cassandra.locator.DynamicEndpointSnitch
        Partitioner: org.apache.cassandra.dht.Murmur3Partitioner
        Schema versions:
                3adce62d-808d-3f1a-ad24-e23220170631: [10.10.10.51, 10.10.10.52, 10.10.10.53]

cass3.deltakappa.com | CHANGED | rc=0 >>
Cluster Information:
        Name: cluster22
        Snitch: org.apache.cassandra.locator.DynamicEndpointSnitch
        Partitioner: org.apache.cassandra.dht.Murmur3Partitioner
        Schema versions:
                3adce62d-808d-3f1a-ad24-e23220170631: [10.10.10.51, 10.10.10.52, 10.10.10.53]

cass1.deltakappa.com | CHANGED | rc=0 >>
Cluster Information:
        Name: cluster22
        Snitch: org.apache.cassandra.locator.DynamicEndpointSnitch
        Partitioner: org.apache.cassandra.dht.Murmur3Partitioner
        Schema versions:
                3adce62d-808d-3f1a-ad24-e23220170631: [10.10.10.51, 10.10.10.52, 10.10.10.53]
```

### Test keyspace is KillrVideo
https://killrvideo.github.io/

Since I did not use their setup, I simple lifted out the schema and data and loaded that up into the keyspace on my cassandra cluster. Thanks Datastax for putting together for us. And, thanks to Patrick McFadin and others at Datastax for the free training videos. I mention Patrick by name only because he is the reason I found out about killrvideo.

This is the "footprint" or the scale of the cassandra data that I'm dealing with in this development test environment.
```
[root@ansible ~]# ansible cassandra -m shell -a "du -sh /var/lib/cassandra/data"
cass2.deltakappa.com | CHANGED | rc=0 >>
115M    /var/lib/cassandra/data
cass3.deltakappa.com | CHANGED | rc=0 >>
114M    /var/lib/cassandra/data
cass1.deltakappa.com | CHANGED | rc=0 >>
105M    /var/lib/cassandra/data
```

### Requirements & Notes
Some of the parts of this solution are not yet optimized. This is literally the results of the  first run through by a newbie to Linux, Python, Ansible, Cassandra, and FOSS in general using one Datastax as a specification document: https://docs.datastax.com/en/archived/cassandra/2.1/cassandra/operations/ops_backup_restore_c.html
Where working from these documents took the most focus...
https://docs.datastax.com/en/archived/cassandra/2.1/cassandra/operations/ops_snapshot_restore_new_cluster.html
https://docs.datastax.com/en/archived/cassandra/2.1/cassandra/operations/ops_backup_snapshot_restore_t.html#ops_backup_snapshot_restore_t

### Ansible Configuration
Following best practices on directory and file organization: https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html
- ansible.cfg - using the default (sample in repo)
- hosts - using the default plus this smaple environment (sample in repo)

### Ansible Configuration Testing
Using the ping module to test inventory (/etc/ansible/hosts file)
```
[root@ansible ~]# ansible cluster22 -m ping
cass2.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
cass1.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
cass3.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[root@ansible ~]# ansible cluster22-deltakappa-dc -m ping
cass3.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
cass1.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
cass2.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
[root@ansible ~]# ansible cass2.deltakappa.com -m ping
cass2.deltakappa.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
```

### Using ad hoc Ansible commands to add other dependencies
install the python-setuptools, python-pip, the cassandra-driver on each cassandra node
- /usr/lib64/python2.7/site-packages (3.15.1)
- /usr/lib/python2.7/site-packages (from cassandra-driver) (1.11.0)
- /usr/lib/python2.7/site-packages (from cassandra-driver) (3.2.0)

```
[root@ansible ~]# ansible cass1.deltakappa.com -m shell -a "yum -y install python-setuptools"
[root@ansible ~]# ansible cass1.deltakappa.com -m shell -a "yum -y install python-pip"
[root@ansible ~]# ansible cass1.deltakappa.com -m shell -a "pip install --upgrade pip"
[root@ansible ~]# ansible cass1.deltakappa.com -m shell -a "pip install cassandra-driver"
```

### Using the playbooks/cluster22/cassandra22x_snapshot.yml playbook
```
ansible-playbook playbooks/cluster22/cassandra22x_snapshot.yml -e "snapshot_uuid=$(uuidgen)" | tee /var/log/ansible/cassandra22x/cluster22/cassandra22x_snapshot_$(date +%Y%d%m%H%M%s).log
```
See var/log/ansible/cassandra22x/cluster22/cassandra22x_snapshot_2018191016431539988994.log to see sample out of a successful run using the sample environment.