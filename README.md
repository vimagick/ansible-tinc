ansible-tinc
============

Tinc Client Deployment for Archlinux.

## How does it work?

- I'm running `tinc-server` in docker container.
- I'm using three `alarmpi`s as `tinc-client`s.
- I need to connect all `tinc-client`s to `tinc-server`.

## Example hosts

```
[vps]
server ansible_ssh_host=foobar.site

[pi]
pi1 ansible_ssh_host=192.168.1.205
pi2 ansible_ssh_host=192.168.1.230
pi3 ansible_ssh_host=192.168.1.236

[all:vars]
ansible_ssh_user=root

[pi:vars]
ansible_python_interpreter=/usr/bin/python2

[pi2:vars]
ansible_ssh_user=alarm
ansible_become=yes
ansible_become_method=su
ansible_become_pass=root
```

## Example playbook.yml

```yaml
---

- hosts: pi
  gather_facts: yes
  roles:
    - {role: tinc, server_dir: /root/fig/tinc/tinc}
```

## Example results

```
$ ansible-playbook -i example-hosts example-playbook.yml

PLAY [pi] *********************************************************************

GATHERING FACTS ***************************************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | fail msg='only support Archlinux'] ******************************
skipping: [pi3]
skipping: [pi2]
skipping: [pi1]

TASK: [tinc | fetch {{groups["vps"] | first}}] ********************************
ok: [pi1 -> server]
ok: [pi2 -> server]
ok: [pi3 -> server]

TASK: [tinc | install tinc] ***************************************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | make directories] ***********************************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | copy tinc.conf] *************************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | copy tinc-up] ***************************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | copy tinc-down] *************************************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | copy {{groups["vps"] | first}}] *********************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | copy {{groups["vps"] | first}}-up] ******************************
ok: [pi3]
ok: [pi1]
ok: [pi2]

TASK: [tinc | copy {{groups["vps"] | first}}-down] ****************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | generate keys] **************************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | modify {{inventory_hostname}}] **********************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

TASK: [tinc | fetch {{inventory_hostname}}] ***********************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | copy {{inventory_hostname}}] ************************************
ok: [pi1 -> server]
ok: [pi3 -> server]
ok: [pi2 -> server]

TASK: [tinc | enable ip forward] **********************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | copy iptables.rules] ********************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | enable iptables service] ****************************************
ok: [pi3]
ok: [pi2]
ok: [pi1]

TASK: [tinc | enable tinc service] ********************************************
ok: [pi2]
ok: [pi3]
ok: [pi1]

PLAY RECAP ********************************************************************
pi1                        : ok=18   changed=0    unreachable=0    failed=0
pi2                        : ok=18   changed=0    unreachable=0    failed=0
pi3                        : ok=18   changed=0    unreachable=0    failed=0
```

```
$ tree hosts
hosts/
├── pi1
├── pi2
├── pi3
└── server
```

> `hosts` directory contains pubkeys of all hosts.
