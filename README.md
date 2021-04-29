# Overview
This role does configures daily backups of postgresql databases.
This role requires root permissions. It must be called as root. This needs to be managed at the ansible or playbook level.

>This role is not intended for large Postgresql Clusters and Infrastructure. For such cases, please look at specialized solutions like barman.

>Backups are stored in {{ backup_dir }}/{{ ansible_fqdn }}/postgresql/{{ postgresql_backup_spec.database_name }}/date

>This role does not manage pg_hba.conf. Thus make sure the host is authorized to connect to the database.

>Use `pg_restore` to restore those backups. Make sure to set the appropriate options.

# Security
Git repositories and inventories must ***never*** expose contain sensitive information such as credentials.
Make sure your sensitive variables are protected using a vault or similiar technology.

Tasks using passwords are not logged.

# Variables

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| postgresql_backup_version | string | no | `13` | The version of postgresql client to use. |
| postgresql_backup_default_host | string | no | `127.0.0.1` | The default hostname or ip to backup from. |
| postgresql_backup_default_port | string | no | `5432` | The default port of the postgresql server. |
| postgresql_backup_default_options | string | no | `""` | The default options to pass to `pg_dump`(ex: "-Fc" for compression) |
| postgresql_backup_uid | integer | no | n.a. | The uid of the backup user to create. |
| postgresql_backup_default_retention | string | no | `30` | The default number of backups to keep. |
| postgresql_backup_default_periodicity | string | no | `OnCalendar=*-*-* 22:00:00` | The default periodicity of backups (every night at 10pm). Systemd timer format. |
| postgresql_backup_mount_type | string | no | `local` | Type of storage that will hold the backup files. Supported types: local, nfs |
| postgresql_backup_dir | string | no | `/tmp/postgresql_backup` | Path where the backups are sent. Is the mount point in case of network storage. |
| postgresql_backup_remote_mount_path | string | no | `nfsserver:/path/to/mount` | The remote path of the mount command. Depends on the protocol. |
| postgresql_backup_spec | list | yes | n.a. | The list of databases to backup. |
| postgresql_backup_spec.postgresql_host | string | no | `postgresql_backup_default_host` | Overrides the postgresql sever host value for this database. |
| postgresql_backup_spec.postgresql_port | string | no | `postgresql_backup_default_port` | Overrides the postgresql sever port value for this database. |
| postgresql_backup_spec.postgresql_options | string | no | `postgresql_backup_default_options` | Overrides the postgresql sever options value for this database. |
| postgresql_backup_spec.database_name | string | yes | n.a. | The name of the database to backup. |
| postgresql_backup_spec.database_user | string | yes | n.a. | The username to use. |
| postgresql_backup_spec.database_password | string | yes | n.a. | The password to use. |
| postgresql_backup_spec.periodicity | string | no | `postgresql_backup_default_periodicity` | Overrides the default periodicity value for this database. |
| postgresql_backup_spec.backup_retention | string | no | `postgresql_backup_default_retention` | Overrides the default retention value for this database. |

# Example of inventory variables

    backup_mount_type: "nfs"
    backup_dir: "/mnt/postgresql_backup"
    remote_mount_path: "192.168.25.2:/exports/backups"
    postgresql_backup_spec:
      - postgresql_host: "192.168.25.3"
        database_name: "pgsql_website_db"
        database_user: "db_bckp_user"
        database_password: "superpassword"
        periodicity: "OnCalendar=*-*-* 1:00:00"


# Reqirements

This role uses the collection based ansible modules which requires:
- ansible >= 2.10
This role depends on the ansible collections:
- ansible.builtin
- ansible.posix

The role configures **systemd timers**, if your host doesn't use systemd, it will fail.

# Automatique Testing

This role is tested using Molecule against:
- Python 3.7 and 3.8
- CentOS 7/8
- Debian 9/10