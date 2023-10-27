# Ansible Variable Precedence

Ansible variable precedence determines the order in which Ansible resolves variable values. When Ansible encounters a variable, it will search for the variable in each variable scope in turn, starting with the most specific scope and ending with the most general scope. If the variable is found in a more specific scope, it will be used in preference to a variable with the same name in a less specific scope.

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

```yaml
# Play variables
webserver_port: 80

# Host variables
webserver_port: 443

# Task
- name: Configure Apache2
  lineinfile:
    path: /etc/apache2/sites-available/000-default
    line: "Listen {{ webserver_port }}"

```

In this example, the `webserver_port` variable is defined in both the play scope and the host scope. However, the play scope has higher precedence than the play scope. This means that the value of the `webserver_port` variable will be 80, which is the value defined in the play scope.

You can also use variable precedence to override the default behavior of Ansible. For example, you can use the `hostvars` scope to override the value of a global variable for a specific host. This can be useful for configuring hosts with different settings.

Here is an example of how to use the `hostvars` scope to override the value of a global variable:

```yaml
# Global variables
ansible_python_interpreter: /usr/bin/python2

# Host variables
[webservers:example.com]
webserver_python_interpreter: /usr/bin/python3

# Task
- name: Install Python3
  apt:
    name: python3
  when: ansible_python_interpreter != hostvars['webserver_python_interpreter']
```

In this example, the `ansible_python_interpreter` variable is defined in the global scope. However, the `webserver_python_interpreter` variable is defined in the host scope for the host `example.com`. This means that the `Install Python3` task will only be executed on the host `example.com`.

```yaml
# Global variable
ansible_python_interpreter: /usr/bin/python2

# Play variable
webserver_port: 80

# Host variable
webserver_port: 443

# Task variable
webserver_port: 8080

# ...

# Task
- name: Configure Apache2
  lineinfile:
    path: /etc/apache2/sites-available/000-default
    line: "Listen {{ webserver_port }}"
```
In this example, the value of the webserver_port variable used in the Configure Apache2 task will be 8080, because the task variable has the highest precedence.

Reference: https://medium.com/trendfingers/variable-precedence-in-ansible-2a3dba7766ab#:~:text=10.,variables%20can't%20be%20overridden.

