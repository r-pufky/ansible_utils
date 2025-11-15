# Ansible Utility Library
Common ansible task patterns that should be supported by ansible.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_utils/blob/main/meta/main.yml)

## Role Variables
Variables are passed directly to the task being called.

[remote_file](https://github.com/r-pufky/ansible_utils/tree/main/tasks/remote_file.yml)

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.lib](https://github.com/r-pufky/ansible_collection_lib) collection.

## Example Playbook
See each task for detailed argument list. Tasks must be explicitly called.

### remote_file (as root)
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
    name: 'r_pufky.lib.utils'
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

[remote_file](https://github.com/r-pufky/ansible_utils/tree/main/tasks/remote_file.yml)

### atomic_ini (as root)
Atomically set each line in a given INI file. This is a separate Atomic write
case compared to `ini_file`.

Used in the case of INI files which contain duplicate keys (e.g. UE4 games).
INI files that contain duplicate keys violate the INI standard. Files are
written to temp file with exclusive disabled, then moved into place, preventing
infinite duplicate key growth in configs.

This should only be used for INI files meeting this specific case; standard
cases should ALWAYS use community.general.ini_file directly in configuration
tasks.

See reference for all supported options from `ini_file`.

``` yaml
- name: 'Config | atomically set ini'
  ansible.builtin.include_role:
    name: 'r_pufky.lib.utils'
    tasks_from: 'atomic_ini.yml'
  vars:
    ini:
      - section: 'OnlineSubsystem'
        option: 'ServerName'
        value: 'Conan Exiles Server'
        state: 'present'
      - section: 'OnlineSubsystemNull'
        option: 'ServerName'
        value: 'Conan Exiles Server'
        state: 'present'
    path: '/opt/steam/games/conan/engine.ini'
    backup: true
    mode: '0755'
    owner: 'steam'
    group: 'steam'
```

[remote_file](https://github.com/r-pufky/ansible_utils/tree/main/tasks/atomic_file.yml)

## Development
Configure [environment](https://r-pufky.github.io/ansible_collection_docs/ansible/environment)

Run all unit tests:
``` bash
molecule test --all
```

### Releases
Major release versions track Debian release versions:

* **[13.x.x](https://github.com/r-pufky/ansible_utils)**: 13 Trixie.
* **[12.x.x](https://github.com/r-pufky/ansible_utils/tree/12.x)**: 12 Bookworm.

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_utils/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
