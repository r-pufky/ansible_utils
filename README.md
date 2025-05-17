# Ansible Utility Library
Common ansible task patterns that should be supported by ansible.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_utils/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_utils/tree/main/defaults/main/)

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.lib](https://github.com/r-pufky/ansible_collection_lib) collection.

## Example Playbook
See each task for detailed argument list. Tasks must be explicitly called.

### remote_file
Explicitly test remote file before applying file changes.

Remote filesystems typically do not have the same or allow the root user to
plow those file permissions (e.g. NFS squashed root or containers). Becoming
'root' locally results in the root user mapped to nobody/nogroup, effectively
locking the task out of modifying permissions even if NO change is required.

This results in ansible stack dumps that are hard to debug due to multiple
system layers on top of vague error messaging from ansible, when simply
setting file permissions (even when file is pre-existing with correct
permissions).

Using `remote_file` prevents this issue, allowing for the consumer to
explicitly set the correct permissions before applying a given role which
modifies these files.

``` yaml
- name: 'Config | manage required directories'
  ansible.builtin.include_role:
    name: 'r-pufky.lib.utils'
    tasks_from: 'remote_file.yml'
  vars:
    file_path: '{{ item }}'
    file_owner: '{{ user }}'
    file_group: '{{ group }}'
    file_mode: '{{ perms }}'
    file_recurse: '{{ recursion }}'
    file_state: 'directory'
  loop:
    - '/etc/service'
    - '/mnt/remote/data/service'
    - '/var/lib/service'
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_docs/blob/main/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_utils/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
