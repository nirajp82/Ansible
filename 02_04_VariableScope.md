# Ansible Variable Scopes

Variables in Ansible have different scopes, determining where they are accessible and where they can be overridden. Understanding these scopes is fundamental for effective playbook and role development.

### Table of Contents

1. **Global Scope**
2. **Playbook Scope**
3. **Task Scope**
4. **Role Scope**
5. **Inventory Scope**

---

## 1. Global Scope

Variables in the global scope are accessible throughout all playbooks and roles. They can be defined in:

- **Ansible Configuration:** Variables set here apply globally to all playbooks and roles.
- **Environment Variables:** Variables set in the environment where Ansible runs.

## 2. Playbook Scope

Variables at the playbook scope are defined directly under the `vars` keyword in a playbook. They are accessible to all tasks within that playbook. These variables are typically used to store configuration values specific to that playbook.

**Example:**

```yaml
---
- hosts: web_servers
  vars:
    http_port: 80
  tasks:
    - name: Ensure Apache is running
      service:
        name: apache2
        state: started
```

## 3. Task Scope

Variables in task scope are defined within a specific task using the `register` keyword. They store the output of the task, such as the result of a command or the content of a file. These variables are specific to the task in which they are defined and can be accessed in subsequent tasks (Tasks that appear after the current task in the playbook.) within the same play.

**Example:**

```yaml
---
- hosts: web_servers
  tasks:
    - name: Task 1 - Get disk space
      command: df -h /
      register: disk_space
    
    - name: Task 2 - Some task that does not use the variable
      debug:
        msg: "This task does not use the disk_space variable"
    
    - name: Task 3 - Display disk space (uses variable from Task 1)
      debug:
        var: disk_space.stdout_lines
```

In the above example, `disk_space` is a variable storing the output of the `df -h /` command, allowing you to access the disk space information in the subsequent task.

## 4. Role Scope

Variables in role scope are defined within a role and are stored in the `defaults/main.yml` file. They are accessible to all tasks within that role. Role variables provide a way to set default values specific to the role, which can be overridden at the playbook level if necessary.

**Example (Inside role/defaults/main.yml):**

```yaml
http_port: 80
```

## 5. Inventory Scope

Variables in inventory scope are defined in inventory files or inventory directories (`group_vars` and `host_vars`). They are specific to hosts or groups in the inventory and override role defaults or playbook-level variables for those hosts or groups.

**Example (Inside inventory file):**

```ini
[web_servers]
server1 ansible_ssh_user=admin
server2 ansible_ssh_user=user
```

Understanding these variable scopes allows you to effectively manage and utilize variables in your Ansible automation, ensuring the right values are used in the right contexts.
