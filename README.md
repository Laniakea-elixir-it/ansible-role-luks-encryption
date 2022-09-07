luks-encryption
=========

This role provides storage encryption using LUKS. It exploits the pyluks python package in order to encrypt and manage external volumes.

This role is based on the [indigo-dc.galaxycloud-os](https://github.com/indigo-dc/ansible-role-galaxycloud-os) role.

Role Variables
--------------

| Variable           | Description                              | Default |
| ------------------ | ---------------------------------------- | ------- |
| storage_encryption | enable/disable storage encryption | true |
| store_passphrase | Where to store passphrase, options are 'vault' or 'locally' | 'vault' |
| export_dir | Export directory where the external volume is mounted | /export |
| pyluks_venv | Path for the venv where pyluks is installed | /opt/pyluks | |
| pyluks_package_name | Name of pyluks package | pyluks |
| pyluks_version | pyluks version installed | 0.0.1b |
| pip version | pip version installed in the venv | 21.3.1 |

## fastluks variables
| Variable           | Description                              | Default |
| ------------------ | ---------------------------------------- | ------- |
| luks_success_file | File indicating encryption success | /var/run/fast-luks-encryption.success |
| volume_setup_success_file | File indicating volume setup success | /var/run/fast-luks-volume-setup.success |
| luks_config_path | Directory containing LUKS configuration files | /etc/luks
| luks_config_file | LUKS configuration file | {{ luks_config_path }}/luks-cryptdev.ini |
| wait_timeout | Time to wait for encryption to finish | 18000 |
| cipher_algorithm | Cipher algorithm | aes-xts-plain64 |
| keysize | Key size | 256 |
| hash_algorithm | Hash algorithm | sha256 |
| device | Device to encrypt | /dev/vdb |
| cryptdev | Device mapper name | crypt |
| mountpoint | Encrypted device mountpoint | /export |
| filesystem | Encrypted device filesystem | ext4 |
| passphrase_length | Encryption passphrase length | 100 |
| vault_encryption_token | Dictionary containing Vault endpoint and wrapping token | {} |
| vault_url | Vault endpoint | {{ vault_encryption_token.endpoint | default("") }} |
| vault_wrapping_token | Vault wrapping token used to write the passphrase | {{ vault_encryption_token.wrapping_token | default("") }} |
| vault_secret_path | Vault path for the passphrase | {{ vault_encryption_path | default("") }} |
| valult_secret_key | Vault key for the passphrase | {{ vault_encryption_key | default("") }} |


## luksctl_api variables
| Variable           | Description                              | Default |
| ------------------ | ---------------------------------------- | ------- |
| enable_luksctl_api | Enable/disable luksctl API configuration | true |
| daemons | List of daemons to be restarted when the volume is unlocked with luksctl API | [] |


Example Playbook
----------------

Test only encryption (save passphrase locally and do not configure luksctl API):
```yml
- hosts: servers
  become: yes
  roles:
    - role: laniakea.luks_encryption
      vars:
        store_passphrase: "locally"
        enable_luksctl_api: false
```

Test encryption with pyluks dev branch:
```yml
- hosts: servers
  become: true
  roles:
    - role: laniakea.luks_encryption
      vars:
        pyluks_package_name: "https://github.com/Laniakea-elixir-it/pyluks/archive/dev.zip"
        vault_encryption_token:
          endpoint: vault_endpoint
          wrapping_token: xxxxx
        vault_encryption_path: path
        vault_encryption_key: key
```

Ecrypt master vm in a cluster with docker (external volume with docker images and shared through nfs)
```yml
- hosts: servers
  become: yes
  roles:
    - role: laniakea.luks_encryption
      vars:
        vault_encyrption_token:
          endpoint: vault_url
          wrapping_token: xxxxx
        vault_encryption_path: path
        vault_encryption_key: secret
        daemons:
          - docker
          - nfs-server
```

License
-------

Apache Licence v2: http://www.apache.org/licenses/LICENSE-2.0

Author Information
------------------
Daniele Colombo (daniele.colombo39@gmail.com)
