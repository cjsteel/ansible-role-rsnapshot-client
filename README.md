# rsnapshot (slave) [![Build Status](https://travis-ci.org/osiell/ansible-rsnapshot-slave.png)](https://travis-ci.org/osiell/ansible-rsnapshot-slave)

Ansible role to configure rsnapshot clients to be backed up by
the rsnapshot server role.

You will probably want to configure and run the the `rsnapshot-server` role before the `rsnapshot-client` role.

* installs rsync
* creates a system user with sudo privileges on the `rsync` program.
* retrieces the public SSH key from the **rsnapshot server**.
* adds the servers public key the the backup users user's authorized_keys file.

## Roadmap

[docs/ROADMAP.md](docs/ROADMAP.md)

## Variables

```yaml
rsnapshot_master_host: backup_servers_hostname
rsnapshot_master_host_user: root                 # frequently root
rsnapshot_master_ssh_key: id_rsa

# User used by the rsnapshot master to connect on the configured host
rsnapshot_slave_user: backupuser
```
## inventory example

When your Ansible controller is nether a backup server nor client and you want to connect as the Ansible controller user.

```yaml
inventory/dev 
[rsnapshot-server]
backup.server.com

[rsnapshot-clients]
backup.client.com
```

### alternative option

When you are running on a backup client and connecting to the server to be configured via a different user (not the Ansible controller user).

```yaml
[rsnapshot-server]
backup.server.com ansible_ssh_user=special_user

[rsnapshot-clients]
backup.client.com ansible_connection=local
```

## Example (Playbook)

```yaml
- name: Hosts to backup
  hosts: rsnapshot-clients
  sudo: yes
  roles:
    - rsnapshot-client
  vars:
    - rsnapshot_server_host: rsnapshot-server
```

or

```yaml
- name: Hosts to backup
  hosts: remote_host_1:remote_host_2
  sudo: yes
  roles:
    - rsnapshot-slave
  vars:
    - rsnapshot_master_host: backup_master
```

See the `rsnapshot-client` role for details.
