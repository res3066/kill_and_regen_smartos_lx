Role Name
=========

kill_and_regen_smartos_lx - ensures that a SmartOS LX zone goes away and gets regenerated properly (including prerequisites like base image, etc).  

Requirements
------------

rs_make_vmadm_root_authorized_keys - which takes a pile of sshkey.pub files and concatenates them into the funky format that vmadm wants in its json.

Role Variables
--------------

Probably belong in group_vars/all.yml for a sparse play:

```yaml
# ubuntu 16.04
smartos_image: '7b5981c4-1889-11e7-b4c5-3f3bdfc9b88b'
#
host_specific_files: "{{ inventory_dir }}/host_files/{{ inventory_hostname }}"
host_root_ssh_keys: "{{ host_specific_files }}/root_ssh_keys"

vmadm_max_physical_memory: 2048
vmadm_quota: 500

vmadm_resolvers:
  - "192.148.252.11"
  - "192.148.252.12"
```

Almost certainly belong in host_vars/zone1.example.com.yml

```yaml
nics:
  - interface: net0
    vlan_id: 100
    nic_tag: vlan
    gateways:
      - 192.148.252.1
    ips:
      - "192.148.252.21/25"
      - "2610:178:1:1::252:21:1/64"
    primary: true
```


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------


inventory:

```yaml
[smartos-headnode]
so1.example.com ansible_host=192.0.2.100 ansible_user=root

[myzones]
zone1.example.com ansible_user=root ansible_host=zone1.example.com global_host=so1.example.com ansible_python_interpreter="/usr/bin/python"
```

playbook:

```yaml
- hosts: myzones
  gather_facts: no
  tasks:
    - import_role:
        name: rs_make_vmadm_root_authorized_keys
      when: nuke_from_orbit is defined

    - import_role:
        name: kill_and_regen_smartos_lx
      when: nuke_from_orbit is defined

    - name: "Waiting for the VM to start..."
      pause: seconds=3
```

License
-------

MIT

Author Information
------------------

rs@cluetrust.com
https://technotes.seastrom.com


