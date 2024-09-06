<p align="center"> <img src="https://user-images.githubusercontent.com/34257858/129839002-15e3f2c7-3f75-46d4-afae-0fd207d7fdde.png" width="100" height="100"></p>

<h1 align="center">
    Ansible Role SSH
</h1>

<p align="center" style="font-size: 1.2rem;">
    This ansible role setup SSH On Ubuntu, CentOS.
</p>

<p align="center">

<a href="https://www.ansible.com">
  <img src="https://img.shields.io/badge/Ansible-2.10-green?style=flat&logo=ansible" alt="Ansible">
</a>
<a href="LICENSE.md">
  <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="Licence">
</a>
<a href="https://ubuntu.com/">
  <img src="https://img.shields.io/badge/ubuntu-20.x-orange?style=flat&logo=ubuntu" alt="Distribution">
</a>
<a href="https://www.centos.org/">
  <img src="https://img.shields.io/badge/CentOS-8-green?style=flat&logo=centos" alt="Distribution">
</a>

## Requirements (collections)

-   ansible.posix
-   ansible.crypto

## Role Variables

### SSH Hardening config

| Variables Name                | Default | Description                                                         |
| ----------------------------- | ------- | ------------------------------------------------------------------- |
| `ssh_secure_config`           | `false` | Secure SSH config                                                   |
| `ssh_port`                    | `22`    | Secure SSH connection port                                          |
| `ssh_max_auth_tries`          | `3`     | Secure SSH limit the maximum number of authentication               |
| `ssh_login_grace_time`        | `3`     | Secure SSH login grace period                                       |
| `ssh_password_authentication` | `"no"`  | Secure SSH connection password authentication                       |
| `ssh_permit_root_login`       | `"yes"` | Secure SSH connection root permissions                              |
| `ssh_allow_users`             | `[]`    | Secure SSH user authentications based on username and/or IP address |
| `ssh_usedns`                  | `"no"`  | Secure SSH connection use dns                                       |
| `ssh_permit_empty_password`   | `"no"`  | Secure SSH connection with empety password                          |
| `ssh_challenge_response_auth` | `"no"`  | Secure SSH connection challege response auth                        |
| `ssh_gss_api_authentication`  | `"no"`  | Secure SSH connection using GSSAPI to authenticate                  |
| `ssh_x11_forwarding`          | `"no"`  | Secure SSH connection X11 forwarding working                        |
| `ssh_allow_tcp_forwarding`    | `"no"`  | Secure SSH port forwarding                                          |
| `ssh_allow_agent_forwarding`  | `"no"`  | Secure SSH simplify deploying to a server                           |
| `ssh_permit_user_env`         | `"no"`  | Secure SSH set variables for remote commands                        |
| `ssh_client_alive_interval`   | `300`   | Secure SSH sets a timeout interval                                  |
| `ssh_client_alive_count_max`  | `3`     | Secure SSH sets the number of server alive messages                 |

### Set Banner

| Name                        | Default    | Description                   |
| --------------------------- | ---------- | ----------------------------- |
| `ssh_custom_banner_enabled` | `false`    | Secure SSH custom banner      |
| `ssh_custom_banner_text`    | `asaplabs` | Secure SSH custom banner text |

### SSH config for users

| Name             | Default | Description                                                                                                                 |
| ---------------- | ------- | --------------------------------------------------------------------------------------------------------------------------- |
| `ssh_user_root`  | `[]`    | Setup SSH for root [sample](#example-playbook)                                                                              |
| `ssh_user_users` | `[]`    | Setup multiple ssh directory with users [sample](#example-playbook)                                                         |
| `ssh_key_type`   | `"rsa"` | Setup ssh key types for root and users `rsa`, `dsa`, `ecda` and `ed25519`.                                                  |
| `ssh_key_size`   | `2048`  | Setup ssh key size [doc](https://docs.ansible.com/ansible/latest/collections/community/crypto/openssh_keypair_module.html). |

## Dependencies

None.

<h2 id="example-playbook">Example Playbook</h2>

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

### Auto generate SSH (private key and public key) setup with `manage_ssh_key: true`

```yaml
- hosts: servers
  vars:
    ssh_secure_config: true
    ssh_port: 2222 # custom ssh port
    ssh_permit_root_login: "no"
    ssh_allow_users:
        - asap
    ssh_user_root:
        - manage_ssh_key: true
          ssh_key_type: rsa
          ssh_key_size: 2048 # only for automatic generate ssh key
          authorized_keys: # not mandatory
            - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
              state: present
          ssh_config_hosts:
            - name: github.com
              host: github.com
              user: git
            - name: gitlab.com
              host: gitlab.com
              user: git
      ssh_user_users:
        - name: vagrant
          manage_ssh_key: true
          ssh_key_type: rsa
          ssh_key_size: 2048 # only for automatic generate ssh key
          authorized_keys: # not mandatory
            - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
              state: present
        ssh_config_hosts:
            - name: github.com
              host: github.com
              user: git
            - name: gitlab.com
              host: gitlab.com
              user: git

  roles:
      - { role: asapdotid.ssh }
```

### Custom setup SSH

```yaml
- hosts: servers
  vars:
      ssh_user_root:
          - manage_ssh_key: false
            ssh_key_type: rsa
            private_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa') }}"
            public_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
            authorized_keys: # not mandatory
                - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/vps_rsa.pub') }}"
                  state: present
            ssh_config_hosts:
                - name: github.com
                  host: github.com
                  user: git
                - name: gitlab.com
                  host: gitlab.com
                  user: git
      ssh_user_users:
          - name: vagrant
            manage_ssh_key: false
            ssh_key_type: rsa
            private_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa') }}"
            public_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
            authorized_keys: # not mandatory
                - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/vps_rsa.pub') }}"
                  state: present
                  # get public keys form github account
                - key: "{{ lookup('url', 'https://github.com/your_github_username.keys', split_lines=False) }}"
                  state: present
            ssh_config_hosts:
                - name: github.com
                  host: github.com
                  user: git
                - name: gitlab.com
                  host: gitlab.com
                  user: git

  roles:
      - { role: asapdotid.ssh }
```

## License

MIT / BSD

## Author Information

[JogjaScript](https://jogjascript.com)

This role was created in 2021 by [Asapdotid](https://github.com/asapdotid).
