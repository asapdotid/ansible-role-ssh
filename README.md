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

## Requirements

None

## Role Variables

| Name                          | Default Value | Description                                         |
| ----------------------------- | ------------- | --------------------------------------------------- |
| `ssh_user_ssh_portroot`       | `"22"`        | Secure SSH connection port.                         |
| `ssh_password_authentication` | `"no"`        | Secure SSH connection password authentication.      |
| `ssh_permit_root_login`       | `"no"`        | Secure SSH connection root permissions.             |
| `ssh_usedns`                  | `"no"`        | Secure SSH connection use dns.                      |
| `ssh_permit_empty_password`   | `"no"`        | Secure SSH connection with empety password.         |
| `ssh_challenge_response_auth` | `"no"`        | Secure SSH connection challege response auth.       |
| `ssh_gss_api_authentication`  | `"no"`        | Secure SSH connection using GSSAPI to authenticate. |
| `ssh_x11_forwarding`          | `"no"`        | Secure SSH connection X11 forwarding working.       |
| `ssh_user_root`               | `[]`          | Setup SSH for root.                                 |
| `ssh_user_users`              | `[]`          | Setup multiple ssh directory with users.            |

## Dependencies

None.

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

### Auto generate SSH (private key and public key) setup with `manage_ssh_key: yes`

```yaml
- hosts: servers
  vars:
    ssh_permit_root_login: yes
    ssh_user_root:
      - manage_ssh_key: yes
        authorized_keys:
          - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
            state: present
    ssh_user_users:
      - name: vagrant
        manage_ssh_key: yes
        authorized_keys:
          - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
            state: present

  roles:
    - { role: asapdotid.ssh }
```

### Custom setup SSH

```yaml
- hosts: servers
  vars:
    ssh_user_root:
      - manage_ssh_key: no
        private_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa') }}"
        public_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
        authorized_keys:
          - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/vps_rsa.pub') }}"
            state: present
    ssh_user_users:
      - name: vagrant
        manage_ssh_key: no
        private_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa') }}"
        public_key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
        authorized_keys:
          - key: "{{ lookup('file', lookup('env','HOME') + '/.ssh/vps_rsa.pub') }}"
            state: present

  roles:
    - { role: asapdotid.ssh }
```

## License

MIT / BSD

## Author Information

[JogjaScript](https://jogjascript.com)

This role was created in 2021 by [Asapdotid](https://github.com/asapdotid).
