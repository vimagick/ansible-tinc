ansible-tinc
============

Tinc Client Deployment for Archlinux.

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

> I'm using alarmpi as tinc-client.

## Example playbook

```
---

- hosts: pi
  gather_facts: yes
  roles:
    - {role: tinc, server_dir: /root/fig/tinc/tinc}
```

> I'm running tinc-server in docker container.
