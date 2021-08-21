Ansible Role: Storage
=========

Role for configure storage on Linux system (manage partitions, LVM volumes groups and logica volumes, filesystems and mount points) on Linux.

Requirements
------------

None.

Role Variables
--------------

Available variables are listed below along with default values (see `defaults\main.yaml`)


Set default filesystem to be used

```yml
storage_default_fstype: ext4
```

Partitions to be created:

```yml
storage_partitions:
  - name: /dev/sdb
    number: 1
    part_end: 4GiB
  - name: /dev/sdb
    number: 2
    flags:
      - lvm
    part_start: 4GiB
    part_end: 8GiB
```
LVM volumen groups to be created

```yml
storage_volumegroups:
  - name: group1
    devices:
      - /dev/sdb2
  - name: group2
    devices:
      - /dev/sdb2
```
LVM logical volumes to be created

```yml
storage_volumes:
  - name: var1
    vg: group1
    size: 16

storage_filesystems:
  - name: /dev/group1/var
    filesystem: ext4
```

Mount points to be created

```yml
storage_mounts:
  - name: /var
    src: /dev/group1/var1
    owner: root
    group: root
    mode: "0755"
    opts: defaults
    boot: yes
    dump: 0
    passno: 2

```

Dependencies
------------

None

Example Playbook
----------------

```yml
---
- hosts: storage-server
  remote_user: ansible
  become: true
  vars:
    storage_partitions:
      - name: /dev/sdb
        number: 1
        part_end: 250MB
      - name: /dev/sdb
        number: 2
        flags:
          - lvm
        part_start: 250MB
        part_end: 2GiB  
      - name: /dev/sdb
        number: 3
        flags:
          - lvm
        part_start: 2GiB
        part_end: 100%
    storage_volumegroups:
      - name: vg_local
        devices:
          - /dev/sdb2
      - name: vg_iscsi
        devices:
          - /dev/sdb3
    storage_volumes:
      - name: vg_iscsi_lv_node1
        vg: vg_iscsi
        size: 500
      - name: vg_iscsi_lv_node2
        vg: vg_iscsi
        size: 500
      - name: vg_iscsi_lv_node3
        vg: vg_iscsi
        size: 500
      - name: vg_iscsi_lv_node4
        vg: vg_iscsi
        size: 500
      - name: vg_local_lv_data
        vg: vg_local
        size: 1024
    storage_filesystems:
      - name: /dev/vg_local/vg_local_lv_data
        filesystem: ext4
    storage_mounts:
      - name: /data
        src: /dev/vg_local/vg_local_lv_data
        owner: root
        group: root
        mode: "0755"
        opts: defaults
        boot: yes
        dump: 0
        passno: 2
  roles:
    - ricsanfre.storage
```
License
-------

MIT/BSD

Author Information
------------------

Ricardo Sanchez (ricsanfre)