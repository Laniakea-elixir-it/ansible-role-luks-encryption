luks-encryption
=========

This role provides storage encryption using LUKS. It exploits the pyluks python package in order to encrypt and manage external volumes.

This role is based on the [indigo-dc.galaxycloud-os](https://github.com/indigo-dc/ansible-role-galaxycloud-os) role.

Role Variables
--------------

``type_of_node``: node type for cluster configuration. master is executed for single VMs and cluster front node. wn is executed on cluster WNs. Default: front

### pyluks variables
``pyluks_venv``: path for the virtualenv in which pyluks is installed. Default: /opt/pyluks

``pyluks_version``: pyluks version installed. Default: 0.0.1

``pip_version``: pip version installed. Default: 21.3.1

### fastluks variables
``luks_success_file``: file indicating encryption success. Default: /var/run/fast-luks-encryption.success

``volume_setup_success_file``: file indicating volume setup success. Default: /var/run/fast-luks-volume-setup.success

``luks_config_path``: path containing LUKS configuration files. Default: /etc/luks

``luks_config_file``: LUKS configuration file path. Default: /etc/luks/luks-cryptdev.ini

``wait_timeout``: time to wait for encryption. Default: 18000 (5 hours)

### Encryption variables
``cipher_algorithm``: cipher algorithm. Default: aes-xts-plain64

``keysize``: key size. Default: 256

``hash_algorithm``: hash algorithm. Default: sha256

``device``: device to encrypt. Default: /dev/vdb

``cryptdev``: device mapper name. Default: crypt

``mountpoint``: mountpoint. Default: /export

``filesystem``: file system. Default: ext4

``passphrase_length``: passphrase length. Default: 8

### Hashicorp Vault variables
``vault_url``: Vault url

``vault_wrapping_token``: Vault wrapping token to write the passphrase

``vault_secret_path``: Vault path for the passphrase

``vault_secret_key``: Vault key for the passphrase

### luksctl_api variables
``infrastructure_configuration``: type of configuration for the master node, possible values are single_vm or cluster. Default: single_vm

``virtualization_type``: type of virtualization, possible value is docker

``node_list``: worker nodes list

``nfs_mountpoint_list``: mountpoints for the worker node



Example Playbook
----------------

Default configuration (single vm)
```yml
- hosts: servers
  roles:
    - role: dacolombo.luks-encryption
      become: yes
```

License
-------

Apache Licence v2: http://www.apache.org/licenses/LICENSE-2.0

Author Information
------------------
Daniele Colombo (daniele.colombo39@gmail.com)
