# Ansible Role: restic

[![Build Status](https://img.shields.io/travis/projectgroup/ansible.projectname.svg?branch=master&style=popout-square)](https://travis-ci.org/projectgroup/ansible.projectname) [![license](https://img.shields.io/github/license/mashape/apistatus.svg?style=popout-square)](https://sbaerlo.ch/licence) [![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-projectname-blue.svg?style=popout-square)](https://galaxy.ansible.com/projectgroup/projectname) [![Ansible Role](https://img.shields.io/ansible/role/d/id.svg?style=popout-square)](https://galaxy.ansible.com/projectgroup/projectname)

## Description
[Restic](https://github.com/restic/restic) is a versatile Go based backup
solution which supports multiple backends, deduplication and incremental
backups.

This role installs restic on a client, configures the backup repositories
and optionally sets up cronjobs to run said backups.
Aditionally, it will setup executable scripts to run a Backup manually.

> This Project borrows from the
> [donat-b/ansible-restic](https://github.com/donat-b/ansible-restic)
> ansible role.
<!-- ## Installation

```bash
ansible-galaxy install restic
``` -->

## Requirements
* bzip2
## Role Variables

| Name                 | Default      | Description                                                                 |
| -------------------- | ------------ | --------------------------------------------------------------------------- |
| `restic_version`     | `'0.9.5'`    | The version of Restic to install                                            |
| `restic_install_dir` | `'/usr/bin'` | Install location for the restic binary                                      |
| `restic_script_dir`  | `'~/restic'` | Location of the generated backup scripts                                    |
| `restic_repos`       | `{}`         | A dictionary of repositories where snapshots are stored                     |
| `restic_backups`     | `[]`         | A list of dictionaries specifying the files and directories to be backed up |
| `restic_create_cron` | `false`      | Should a cronjob be created for each backup                                 |

### Repos
Restic stores data in repositories. You have to specify at least one repository
to be able to use this role. A repository can be local or remote (see the
official [documentation](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html)).

> **Using an SFTP repository**
>
> For using an SFTP backend, the user needs passwordless access to the host.
> Please make sure to distribute ssh keys accordingly, as this is outside of
> the scope of this role.

Available variables:

| Name       | Required | Description                                                                                                                                                                                                                       |
| ---------- |:--------:| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `location` |   yes    | The location of the Backend. Currently, [Local](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#local) and [SFTP](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#sftp) are supported |
| `password` |   yes    | The password used to secure this repository                                                                                                                                                                                       |

Example:
```yaml
restic_repos:
  local:
    location: /srv/restic-repo
    password: securepassword1
  remote:
    location: sftp:user@host:/srv/restic-repo
    password: securepassword2
```

### Backups
A backup specifies a directory or file to be backuped. A backup is written to a
Repository defined in `restic_repos`.

Available variables:

| Name             |           Required            | Description                                                                                                             |
| ---------------- |:-----------------------------:| ----------------------------------------------------------------------------------------------------------------------- |
| `name`           |              yes              | The name of this backup. Used together with pruning and needs to be unique.                                             |
| `src`            |              yes              | The source directory or file                                                                                            |
| `stdin`          |              no               | Is this backup created from a [stdin](https://restic.readthedocs.io/en/stable/040_backup.html#reading-data-from-stdin)? |
| `stdin_cmd`      | no (yes if `stdin` == `true`) | The command to produce the stdin.                                                                                       |
| `stdin_filename` |              no               | The filename used in the repository.                                                                                    |
| `tags`           |              no               | Array of default tags                                                                                                   |
| `keep-last`      |              no               | If set, only keeps the last n snapshots.                                                                                                                        |

## Dependencies
none
## Example Playbook

```yml
- hosts: all
  roles:
    - restic
```

## Author

- Matthias Leutenegger

## License

This project is under the MIT License. See the [LICENSE](https://sbaerlo.ch/licence) file for the full license text.

## Copyright

(c) 2019, Arillso
