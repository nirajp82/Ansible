# Ansible Variable Precedence

This guide outlines the precedence of variables in Ansible, helping you understand which values take precedence when conflicts arise.

## Table of Contents

1. [Role Defaults](#1-role-defaults)
2. [Inventory Variables](#2-inventory-variables)
3. [Inventory Group_vars and Host_vars](#3-inventory-group_vars-and-host_vars)
4. [Playbook Group_vars and Host_vars](#4-playbook-group_vars-and-host_vars)
5. [Host Facts and Registered Vars](#5-host-facts-and-registered-vars)
6. [Set Facts](#6-set-facts)
7. [Play Vars, Play vars_prompt, and Play vars_files](#7-play-vars-play_vars_prompt-and-play_vars_files)
8. [Role and Include Vars](#8-role-and-include-vars)
9. [Block Variables and Task Variables](#9-block-variables-and-task-variables)
10. [Extra Vars](#10-extra-vars)

### 1. Role Defaults
Role defaults have the lowest precedence. They are defined in the role's `defaults/main.yml` file.

Example:
```yaml
# roles/role_name/defaults/main.yml
---
nginx_port: 80
```

### 2. Inventory Variables
Variables defined in the inventory file relate to specific hosts.

Example:
```ini
# inventory.ini

web_server ansible_host=192.168.1.1 nginx_port=8080
```

### 3. Inventory Group_vars and Host_vars
These are specified for groups and hosts inside the inventory directory.

Example (group_vars/webservers.yml):
```yaml
---
nginx_port: 8081
```

### 4. Playbook Group_vars and Host_vars
Defined in the playbook directory, these variables override inventory variables.

Example (playbook/group_vars/webservers.yml):
```yaml
---
nginx_port: 8082
```

### 5. Host Facts and Registered Vars
Host facts are retrieved from remote systems. Registered vars store task output.

Example:
```yaml
- name: Register output
  command: some_command
  register: command_output
```

### 6. Set Facts
Set using `set_fact` module, these variables take higher precedence.

Example:
```yaml
- name: Set fact
  set_fact:
    nginx_port: 8083
```

### 7. Play Vars, Play vars_prompt, and Play vars_files
Variables defined in plays, via vars_prompt, or loaded from files take precedence over lower-level variables.

Example:
```yaml
- hosts: webservers
  vars:
    nginx_port: 8084
```

### 8. Role and Include Vars
Specified in role/include definitions, these have higher precedence.

Example:
```yaml
- hosts: webservers
  roles:
    - role: nginx
      vars:
        nginx_port: 8085
```

### 9. Block Variables and Task Variables
Variables defined in a playbook’s block or task level supersede lower-level variables.

Example:
```yaml
- name: Set nginx_port in block
  block:
    - name: Dummy task
      command: echo 'Hello, World!'
      vars:
        nginx_port: 8086
```

### 10. Extra Vars
Variables passed via the command line, known as ‘extra vars’, have the highest precedence and can’t be overridden.

Example:
```bash
ansible-playbook playbook.yml --extra-vars "nginx_port=8087"
```
