# Ansible Role: restic

> **Beta:** This role is in beta status.

[![Build Status](https://img.shields.io/travis/arillso/ansible.restic.svg?branch=master&style=popout-square)](https://travis-ci.org/arillso/ansible.restic) [![license](https://img.shields.io/github/license/mashape/apistatus.svg?style=popout-square)](https://sbaerlo.ch/licence) [![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-restic-blue.svg?style=popout-square)](https://galaxy.ansible.com/arillso/restic) [![Ansible Role](https://img.shields.io/ansible/role/d/42773.svg?style=popout-square)](https://galaxy.ansible.com/arillso/restic)

## Description
[Restic](https://github.com/restic/restic) is a versatile Go based backup
solution which supports multiple backends, deduplication and incremental
backups.

This role installs restic on a client, configures the backup repositories
and optionally sets up cronjobs to run said backups.
Aditionally, it will setup executable scripts to run a Backup manually.

> This Project borrows heavily from the
> [donat-b/ansible-restic](https://github.com/donat-b/ansible-restic)
> ansible role. We try to make this role more semver deployment friendly
> by allowing to use version tags and keep these snapshots and adapting the
> automated backup definition for use with windows systems.

### Backup Scripts
This role will create a backup script and a file with credentials usable with the `source` command on linux for each backup in the `restic_script_dir`.
These executable scripts can be used to manually trigger a backup action, but
are also used for automated backups if you have set `restic_create_cron` to true.
make sure to not change the files manually, as this can interfere with your
backups quite a bit.

on Linux, if you want to take a manual snapshot, you can run the backup like this:
```bash
$ /path/to/backup/script/backup-example.sh
```
by default, such a snapshot will be given the tag `manual`, so you can distinguish
them from automatically created snapshots. You can also append more tags by
simply appending them:
```bash
$ /path/to/backup/script/backup-example.sh --tag deployment
```

### Access scripts
```bash
. /path/to/backup/script/access-example.sh
restic snapshots
```

### Restore scripts
```bash
# This will restore the latest backup
/path/to/backup/script/restore-example.sh
```

### CRON / Scheduled Tasks
In order to make use of defined backups, they can be automatically setup as
scheduled tasks. You have to be aware of the fact that (on linux systems at
least) you need to have administrator permissions for configuring such an action.

If you cannot use the automatic creation of the tasks, you can still make use
of the generated scripts. If you are for example on a shared hosting server
and can define a cronjob via a webinterface, simply add each backup file to
be executed. Make sure to prefix the command with `CRON=true` to imply that the
snapshot was created via a scheduled task:
```bash
CRON=true /path/to/backup/script/backup-example.sh
```
## Installation

```bash
ansible-galaxy install arillso.restic
```
## Requirements
* bzip2
## Role Variables

| Name                   | Default              | Description                                                                 |
| ---------------------- | -------------------- | --------------------------------------------------------------------------- |
| `restic_url`           | `undefined`          | The URL to download restic from. Use this variable to overwrite the default |
| `restic_version`       | `'0.9.6'`            | The version of Restic to install                                            |
| `restic_download_path` | `'/opt/restic'`      | Download location for the restic binary                                     |
| `restic_install_path`  | `'/usr/local/bin'`   | Install location for the restic binary                                      |
| `restic_script_dir`    | `'~/restic'`         | Location of the generated backup scripts                                    |
| `restic_repos`         | `{}`                 | A dictionary of repositories where snapshots are stored                     |
| `restic_backups`       | `[]`                 | A list of dictionaries specifying the files and directories to be backed up |
| `restic_create_cron`   | `false`              | Should a cronjob be created for each backup                                 |
| `restic_dir_owner`     | `'{{ansible_user}}'` | The owner of all created dirs                                               |
| `restic_dir_group`     | `'{{ansible_user}}'` | The group of all created dirs                                               |

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

| Name                    | Required | Description                                                                                                                                                                                                                                                                                                             |
| ----------------------- |:--------:| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `location`              |   yes    | The location of the Backend. Currently, [Local](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#local), [SFTP](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#sftp) and [S3](https://restic.readthedocs.io/en/stable/030_preparing_a_new_repo.html#amazon-s3) are supported |
| `password`              |   yes    | The password used to secure this repository                                                                                                                                                                                                                                                                             |
| `init`                  |    no    | Describes if the repository should be initialized or not. Use `false` if you are backuping to an already existing repo.                                                                                                                                                                                                 |
| `aws_access_key`        |    no    | The access key for the S3 backend                                                                                                                                                                                                                                                                                       |
| `aws_secret_access_key` |    no    | The secret access key for the S3 backend                                                                                                                                                                                                                                                                                                                        |
| `aws_default_region` |    no    | The desired region for the S3 backend                                                                                                                                                                                                                                                                                                                        |

Example:
```yaml
restic_repos:
  local:
    location: /srv/restic-repo
    password: securepassword1
    init: true
  remote:
    location: sftp:user@host:/srv/restic-repo
    password: securepassword2
    init: true
  remote_foobar_s3:
    location: "s3:s3.amazonaws.com/foobar"
    password: securepassword3
    init: true
    aws_access_key: "{{ access_key_from_vault }}"
    aws_secret_access_key: "{{ secret_access_key_from_vault }}"
    aws_default_region: us-east-2

restic_backups:
  data:
    name: data
    src: /data
    repo: remote_foobar_s3 # NOTE: this must match a key in the restic_repos dict
    auto_restore: true
    scheduled: true
    schedule_minute: 1
    schedule_hour: "{{ 24 | random(seed=inventory_hostname) }}"
    schedule_weekday: '*'
    schedule_month: '*'
    prune: yes
    keep_daily: 7
    keep_weekly: 1
    keep_monthly: 1
    keep_yearly: 1

```

### Backups
A backup specifies a directory or file to be backuped. A backup is written to a
Repository defined in `restic_repos`.

Available variables:

| Name               |      Required (Default)       | Description                                                                                                                                                                  |
| ------------------ |:-----------------------------:| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`             |              yes              | The name of this backup. Used together with pruning and scheduling and needs to be unique.                                                                                   |
| `repo`             |              yes              | The name of the repository to backup to.                                                                                                                                     |
| `src`              |              yes              | The source directory or file                                                                                                                                                 |
| `auto_restore`     |              no               | This will auto restore the latest backup in the event that a state file does not exist in /var/restic-auto-restore                                                           |
| `stdin`            |              no               | Is this backup created from a [stdin](https://restic.readthedocs.io/en/stable/040_backup.html#reading-data-from-stdin)?                                                      |
| `stdin_cmd`        | no (yes if `stdin` == `true`) | The command to produce the stdin.                                                                                                                                            |
| `stdin_filename`   |              no               | The filename used in the repository.                                                                                                                                         |
| `tags`             |              no               | Array of default tags                                                                                                                                                        |
| `keep_last`        |              no               | If set, only keeps the last n snapshots.                                                                                                                                     |
| `keep_hourly`      |              no               | If set, only keeps the last n hourly snapshots.                                                                                                                              |
| `keep_daily`       |              no               | If set, only keeps the last n daily snapshots.                                                                                                                               |
| `keep_weekly `     |              no               | If set, only keeps the last n weekly snapshots.                                                                                                                              |
| `keep_monthly`     |              no               | If set, only keeps the last n monthly snapshots.                                                                                                                             |
| `keep_yearly `     |              no               | If set, only keeps the last n yearly snapshots.                                                                                                                              |
| `keep_within`      |              no               | If set, only keeps snapshots in this time period.                                                                                                                            |
| `keep_tag`         |              no               | If set, keep snapshots with this tags. Make sure to specify a list.                                                                                                          |
| `prune`            |         no (`false`)          | If `true`, the `restic forget` command in the script has the [`--prune` option](https://restic.readthedocs.io/en/stable/060_forget.html#removing-backup-snapshots) appended. |
| `scheduled`        |         no (`false`)          | If `restic_create_cron` is set to `true`, this backup is scheduled.                                                                                                          |
| `schedule_minute`  |           no (`*`)            | Minute when the job is run. ( 0-59, *, */2, etc )                                                                                                                            |
| `schedule_hour`    |           no (`*`)            | Hour when the job is run. ( 0-23, *, */2, etc )                                                                                                                              |
| `schedule_weekday` |           no (`*`)            | Weekday when the job is run.  ( 0-6 for Sunday-Saturday, *, etc )                                                                                                            |
| `schedule_month`   |           no (`*`)            | Month when the job is run. ( 1-12, *, */2, etc )                                                                                                                             |

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
