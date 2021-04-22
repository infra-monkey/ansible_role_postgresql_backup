# Overview
This role does configures daily backups of postgresql databases.
This role requires root permissions. It must be called as root. This needs to be managed at the ansible or playbook level.

# Security
Git repositories and inventories must expose contain sensitive information such as credentials.
Make sure your sensitive variables are protected using a vault or similiar technology.

# Variables

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| postgresql_version | string | no | `13` | The version of postgresql client to use. |
| postgresql_default_host | string | no | `127.0.0.1` | The default hostname or ip to backup from. |
| postgresql_default_port | string | no | `5432` | The default port of the postgresql server. |
| default_backup_retention | string | no | `30` | The default number of backups to keep. |
| default_periodicity | string | no | `OnCalendar=*-*-* 22:00:00` | The default periodicity of backups. Systemd timer format. |
| backup_mount_type | string | no | `local` | Type of storage that will hold the backup files; Supported types: local, nfs |
| backup_dir | string | no | `/tmp/postgresql_backup` | Path where the backups are sent. Is the mount point in case of network storage. |
| remote_mount_path | string | no | `nfsserver:/path/to/mount` | The remote path of the mount command. Depends on the protocol. |
| postgresql_backup_spec | list | yes | n.a. | The list of databases to backup. |
| postgresql_backup_spec.host | string | no | `postgresql_default_host` | desqcription |
| postgresql_backup_spec.port | string | no | `postgresql_default_port` | desqcription |
| postgresql_backup_spec.database_name | string | yes | n.a. | desqcription |
| postgresql_backup_spec.database_user | string | yes | n.a. | desqcription |
| postgresql_backup_spec.database_password | string | yes | n.a. | desqcription |
| postgresql_backup_spec.periodicity | string | no | `default_periodicity` | Overrides the default periodicity value for this database. |
| postgresql_backup_spec.backup_retention | string | no | `default_backup_retention` | Overrides the default retention value for this database. |

# Reqirements

This role uses the collection based ansible modules which requires:
- ansible >= 2.10
This role depends on the ansible collections:
- ansible.builtin
- ansible.posix

# Automatique Testing

This role is tested using Molecule against:
- Python 3.7 and 3.8
- CentOS 7/8
- Debian 9/10