# Example Role

This is an example role to demonstrate the multi-role repository structure.

## Description

Replace this with a description of what your role does.

## Requirements

- Ansible >= 2.9
- Target OS: Ubuntu 20.04+, Debian 11+

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Add your variables here
```

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Use example role
  hosts: servers
  become: true

  roles:
    - role: example-role
```

## License

MIT

## Author Information

Your Name
