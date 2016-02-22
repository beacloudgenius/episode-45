vagrant up

vagrant status                                                                                                                              master ✚
Current machine states:

mgmt                      active (digital_ocean)
lb                        active (digital_ocean)
web1                      active (digital_ocean)
web2                      active (digital_ocean)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific

vagrant ssh mgmt

alternatively,

ssh root@mgmt.cloudgeni.us

ls -l /vagrant
total 20
-rw-r--r-- 1 501 staff  669 Feb 21 13:49 bootstrap-mgmt.sh
drwxr-xr-x 5 501 staff 4096 Feb 21 01:32 examples
-rw-r--r-- 1 501 staff  867 Feb 21 00:58 README
-rw-r--r-- 1 501 staff  205 Feb 21 00:58 README.md
-rw-r--r-- 1 501 staff 2026 Feb 21 23:45 Vagrantfile

ansible --version
ansible 2.0.0.2
  config file = /root/ansible.cfg
  configured module search path = Default w/o overrides

cat ansible.cfg
[defaults]
hostfile = /root/inventory.ini

ping lb.cloudgeni.us
PING lb.cloudgeni.us (45.55.14.241) 56(84) bytes of data.
64 bytes from lb.cloudgeni.us (45.55.14.241): icmp_seq=1 ttl=64 time=1.34 ms
64 bytes from lb.cloudgeni.us (45.55.14.241): icmp_seq=2 ttl=64 time=0.531 ms
^C
--- lb.cloudgeni.us ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.531/0.938/1.346/0.408 ms

ping web1
PING web1.cloudgeni.us (45.55.14.241) 56(84) bytes of data.
64 bytes from lb.cloudgeni.us (45.55.14.241): icmp_seq=1 ttl=64 time=3.74 ms
64 bytes from lb.cloudgeni.us (45.55.14.241): icmp_seq=2 ttl=64 time=0.478 ms
--- web1.cloudgeni.us ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 0.478/1.603/3.742/1.513 ms

ping web2
PING web2.cloudgeni.us (104.131.145.209) 56(84) bytes of data.
64 bytes from web2.cloudgeni.us (104.131.145.209): icmp_seq=1 ttl=63 time=1.10 ms
64 bytes from web2.cloudgeni.us (104.131.145.209): icmp_seq=2 ttl=63 time=0.593 ms
--- web2.cloudgeni.us ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3001ms
rtt min/avg/max/mdev = 0.508/0.686/1.107/0.245 ms

ls -l /etc/ansible
total 20
-rw-r--r-- 1 root root 10301 Jan 14 17:33 ansible.cfg
-rw-r--r-- 1 root root  1016 Jan 14 17:33 hosts
drwxr-xr-x 2 root root  4096 Jan 14 19:21 roles


ssh web1
The authenticity of host 'web1 (45.55.14.241)' can't be established.
ECDSA key fingerprint is 04:c4:4d:4a:8d:8a:00:fb:f8:dd:ef:72:66:cc:39:98.
Are you sure you want to continue connecting (yes/no)? no
Host key verification failed.

ssh-keyscan web1
# web1 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
web1 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDGc7lqhmgXEkzyyoNgroQca6YjFr7T9QZEeMdC41WZkpNzBgwGVzhlEhW3Hi4hT+Sk8O6hrOM24hGUexiaCznfBm8bd/hTJDqBGn4jLH+YWN8kH4rvFRI2L0JVM5YDgZgP0/UTFwemX+XDgx5b5y/R18eu408n/vzZMSEZWns5XuIoHV7uyg4AKAHsAdPZS1XprPEB3xWILXcdQRyXRQ6ww0S7AEGxL2crZ7eqik2PcQXapmyhpVkJ+cOEWwToJHDpmFTfPSQiOPb+8zukxOVMvfO+0/DiTXvm8T8dQdtemWrCoYfaMtjsgP80lnX1Rc3wEl3bNL1r+5ZiWB11yhv9
# web1 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
web1 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM0KXGcjHs08hYnOZRXtbv5Ml0oNMUzSfc9jEqFXnC5ZWgZZNKGWO6mAyiiF/V9Dz5TSj4naRljDVHSYJ6vsk+4=

ssh-keyscan lb web1 web2 >> .ssh/known_hosts
# lb SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
# lb SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
# web1 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
# web1 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
# web2 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6
# web2 SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.6


cat .ssh/known_hosts

cat inventory.ini
[lb]
lb

[web]
web1
web2
#web3
#web4
#web5
#web6
#web7
#web8
#web9


ansible all -m ping
lb | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
web1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
web2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

cat e45-ssh-addkey.yml
---
- hosts: all
  become: yes
  gather_facts: no
  remote_user: root

  tasks:

  - name: install ssh key
    authorized_key: user=root
                    key="{{ lookup('file', '/root/.ssh/id_rsa.pub') }}"
                    state=present

ansible-playbook e45-ssh-addkey.yml


netstat -nap|grep EST
tcp        0     36 104.236.145.41:22       67.183.157.68:36335     ESTABLISHED 1948/0          
tcp        0      0 104.236.145.41:59462    104.236.163.243:22      ESTABLISHED 2237/ansible-ssh-we
tcp        0      0 104.236.145.41:42933    104.236.150.54:22       ESTABLISHED 2231/ansible-ssh-lb
tcp        0      0 104.236.145.41:42857    104.236.157.189:22      ESTABLISHED 2236/ansible-ssh-we


ansible all -m ping
web2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
web1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
lb | SUCCESS => {
    "changed": false,
    "ping": "pong"
}


ansible web1 -m apt -a "name=ntp state=installed"

web1 | SUCCESS => {
    "cache_update_time": 0,
    "cache_updated": false,
    "changed": true,
    "stderr": "",
    "stdout": "Reading package lists...\nBuilding dependency tree...\nReading state information...\nThe following extra packages will be installed:\n  libopts25\nSuggested packages:\n  ntp-doc\nThe following NEW packages will be installed:\n  libopts25 ntp\n0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.\nNeed to get 474 kB of archives.\nAfter this operation, 1,677 kB of additional disk space will be used.\nGet:1 http://mirrors.digitalocean.com/ubuntu/ trusty/main libopts25 amd64 1:5.18-2ubuntu2 [55.3 kB]\nGet:2 http://mirrors.digitalocean.com/ubuntu/ trusty-updates/main ntp amd64 1:4.2.6.p5+dfsg-3ubuntu2.14.04.8 [419 kB]\nFetched 474 kB in 0s (903 kB/s)\nSelecting previously unselected package libopts25:amd64.\n(Reading database ... 86819 files and directories currently installed.)\nPreparing to unpack .../libopts25_1%3a5.18-2ubuntu2_amd64.deb ...\nUnpacking libopts25:amd64 (1:5.18-2ubuntu2) ...\nSelecting previously unselected package ntp.\nPreparing to unpack .../ntp_1%3a4.2.6.p5+dfsg-3ubuntu2.14.04.8_amd64.deb ...\nUnpacking ntp (1:4.2.6.p5+dfsg-3ubuntu2.14.04.8) ...\nProcessing triggers for ureadahead (0.100.0-16) ...\nProcessing triggers for man-db (2.6.7.1-1ubuntu1) ...\nSetting up libopts25:amd64 (1:5.18-2ubuntu2) ...\nSetting up ntp (1:4.2.6.p5+dfsg-3ubuntu2.14.04.8) ...\n * Starting NTP server ntpd\n   ...done.\nProcessing triggers for libc-bin (2.19-0ubuntu6.6) ...\nProcessing triggers for ureadahead (0.100.0-16) ...\n",
    "stdout_lines": [
        "Reading package lists...",
        "Building dependency tree...",
        "Reading state information...",
        "The following extra packages will be installed:",
        "  libopts25",
        "Suggested packages:",
        "  ntp-doc",
        "The following NEW packages will be installed:",
        "  libopts25 ntp",
        "0 upgraded, 2 newly installed, 0 to remove and 0 not upgraded.",
        "Need to get 474 kB of archives.",
        "After this operation, 1,677 kB of additional disk space will be used.",
        "Get:1 http://mirrors.digitalocean.com/ubuntu/ trusty/main libopts25 amd64 1:5.18-2ubuntu2 [55.3 kB]",
        "Get:2 http://mirrors.digitalocean.com/ubuntu/ trusty-updates/main ntp amd64 1:4.2.6.p5+dfsg-3ubuntu2.14.04.8 [419 kB]",
        "Fetched 474 kB in 0s (903 kB/s)",
        "Selecting previously unselected package libopts25:amd64.",
        "(Reading database ... 86819 files and directories currently installed.)",
        "Preparing to unpack .../libopts25_1%3a5.18-2ubuntu2_amd64.deb ...",
        "Unpacking libopts25:amd64 (1:5.18-2ubuntu2) ...",
        "Selecting previously unselected package ntp.",
        "Preparing to unpack .../ntp_1%3a4.2.6.p5+dfsg-3ubuntu2.14.04.8_amd64.deb ...",
        "Unpacking ntp (1:4.2.6.p5+dfsg-3ubuntu2.14.04.8) ...",
        "Processing triggers for ureadahead (0.100.0-16) ...",
        "Processing triggers for man-db (2.6.7.1-1ubuntu1) ...",
        "Setting up libopts25:amd64 (1:5.18-2ubuntu2) ...",
        "Setting up ntp (1:4.2.6.p5+dfsg-3ubuntu2.14.04.8) ...",
        " * Starting NTP server ntpd",
        "   ...done.",
        "Processing triggers for libc-bin (2.19-0ubuntu6.6) ...",
        "Processing triggers for ureadahead (0.100.0-16) ..."
    ]
}

ansible web1 -m apt -a "name=ntp state=installed"
web1 | SUCCESS => {
    "cache_update_time": 0,
    "cache_updated": false,
    "changed": false
}


ls -l files/
total 8
-rw-r--r-- 1 501 staff 504 Feb 21 00:58 ntp.conf
-rw-r--r-- 1 501 staff 417 Feb 21 00:58 ntp.conf.j2

ls -l
total 68
-rw-r--r-- 1 501 staff   42 Feb 21 13:49 ansible.cfg
-rw-r--r-- 1 501 staff  404 Feb 21 13:50 e45-ntp-install.yml
-rw-r--r-- 1 501 staff  113 Feb 21 01:29 e45-ntp-remove.yml
-rw-r--r-- 1 501 staff  456 Feb 21 13:50 e45-ntp-template.yml
-rw-r--r-- 1 501 staff  243 Feb 22 00:29 e45-ssh-addkey.yml
-rw-r--r-- 1 501 staff   83 Feb 21 01:30 e46-role-common.yml
-rw-r--r-- 1 501 staff  109 Feb 21 01:30 e46-role-lb.yml
-rw-r--r-- 1 501 staff  188 Feb 21 01:30 e46-role-site.yml
-rw-r--r-- 1 501 staff  137 Feb 21 01:30 e46-role-web.yml
-rw-r--r-- 1 501 staff 1239 Feb 21 01:31 e46-site.yml
-rw-r--r-- 1 501 staff  103 Feb 21 00:58 e47-parallel.yml
-rw-r--r-- 1 501 staff 2018 Feb 21 01:31 e47-rolling.yml
-rw-r--r-- 1 501 staff  115 Feb 21 00:58 e47-serial.yml
drwxr-xr-x 2 501 staff 4096 Feb 21 00:58 files
-rw-r--r-- 1 501 staff   67 Feb 21 00:58 inventory.ini
drwxr-xr-x 5 501 staff 4096 Feb 21 00:58 roles
drwxr-xr-x 2 501 staff 4096 Feb 21 02:02 templates

cat files/ntp.conf
driftfile /var/lib/ntp/ntp.drift
statistics loopstats peerstats clockstats
filegen loopstats file loopstats type day enable
filegen peerstats file peerstats type day enable
filegen clockstats file clockstats type day enable
server 0.ubuntu.pool.ntp.org
server 1.ubuntu.pool.ntp.org
server 2.ubuntu.pool.ntp.org
server 3.ubuntu.pool.ntp.org
server ntp.ubuntu.com
restrict -4 default kod notrap nomodify nopeer noquery
restrict -6 default kod notrap nomodify nopeer noquery
restrict 127.0.0.1
restrict ::1


ansible web1 -m copy -a "src=/root/files/ntp.conf dest=/etc/ntp.conf mode=644 owner=root group=root"
web1 | SUCCESS => {
    "changed": true,
    "checksum": "f1f51d84bd084c9acbc1a1827b70860db2117ae4",
    "dest": "/etc/ntp.conf",
    "gid": 0,
    "group": "root",
    "md5sum": "5b7b1e1e54f33c6948335335ab03f423",
    "mode": "0644",
    "owner": "root",
    "size": 504,
    "src": "/root/.ansible/tmp/ansible-tmp-1456119197.98-9645516965163/source",
    "state": "file",
    "uid": 0
}


ansible web1 -m copy -a "src=/root/files/ntp.conf dest=/etc/ntp.conf mode=644 owner=root group=root"

web1 | SUCCESS => {
    "changed": false,
    "checksum": "f1f51d84bd084c9acbc1a1827b70860db2117ae4",
    "dest": "/etc/ntp.conf",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "/etc/ntp.conf",
    "size": 504,
    "state": "file",
    "uid": 0
}


ansible web1 -m service -a "name=ntp state=restarted"


ansible all -m shell -a "uptime"
web1 | SUCCESS | rc=0 >>
 00:34:15 up 16 min,  1 user,  load average: 0.00, 0.02, 0.03

lb | SUCCESS | rc=0 >>
 00:34:16 up 17 min,  1 user,  load average: 0.00, 0.01, 0.03

web2 | SUCCESS | rc=0 >>
 00:34:16 up 15 min,  1 user,  load average: 0.00, 0.01, 0.05

ansible all -m shell -a "uname -a"
web2 | SUCCESS | rc=0 >>
Linux web2.cloudgeni.us 3.13.0-77-generic #121-Ubuntu SMP Wed Jan 20 10:50:42 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

web1 | SUCCESS | rc=0 >>
Linux web1.cloudgeni.us 3.13.0-77-generic #121-Ubuntu SMP Wed Jan 20 10:50:42 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

lb | SUCCESS | rc=0 >>
Linux lb.cloudgeni.us 3.13.0-77-generic #121-Ubuntu SMP Wed Jan 20 10:50:42 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux


ansible web -m shell -a "/sbin/reboot"


cat e45-ntp-install.yml


---
- hosts: all
  become: yes
  gather_facts: no

  tasks:

  - name: install ntp
    apt: name=ntp state=installed update_cache=yes

  - name: write our ntp.conf
    copy: src=/root/files/ntp.conf dest=/etc/ntp.conf mode=644 owner=root group=root
    notify: restart ntp

  - name: start ntp
    service: name=ntp state=started

  handlers:

  - name: restart ntp
    service: name=ntp state=restarted

ansible-playbook e45-ntp-install.yml

PLAY ***************************************************************************

TASK [install ntp] *************************************************************
ok: [web1]
changed: [lb]
changed: [web2]

TASK [write our ntp.conf] ******************************************************
ok: [web1]
changed: [lb]
changed: [web2]

TASK [start ntp] ***************************************************************
ok: [web2]
ok: [lb]
ok: [web1]

RUNNING HANDLER [restart ntp] **************************************************
changed: [lb]
changed: [web2]

PLAY RECAP *********************************************************************
lb                         : ok=4    changed=3    unreachable=0    failed=0   
web1                       : ok=3    changed=0    unreachable=0    failed=0   
web2                       : ok=4    changed=3    unreachable=0    failed=0      


Gathering Facts

ansible web1 -m setup | less

press q to quit out of that.

ansible web1 -m setup -a "filter=ansible_distribution"

ansible web1 -m setup -a "filter=ansible_distribution*"

ansible web1 -m setup -a "filter=ansible_all_ipv4_addresses"
web1 | SUCCESS => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "104.236.157.189",
            "10.12.0.51"
        ]
    },
    "changed": false
}


cat e45-ntp-remove.yml
---
- hosts: all
  become: yes
  gather_facts: no

  tasks:

  - name: remove ntp
    apt: name=ntp state=absent


ansible-playbook e45-ntp-remove.yml

PLAY ***************************************************************************

TASK [remove ntp] **************************************************************
changed: [lb]
changed: [web2]
changed: [web1]

PLAY RECAP *********************************************************************
lb                         : ok=1    changed=1    unreachable=0    failed=0   
web1                       : ok=1    changed=1    unreachable=0    failed=0   
web2                       : ok=1    changed=1    unreachable=0    failed=0       
