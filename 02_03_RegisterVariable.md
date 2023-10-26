## Ansible Variables: Registering Variables and Variable Precedence

### Table of Contents

1. **Registering Variables**
2. **Variable Precedence in Ansible**

---

## 1. Registering Variables

In Ansible, you can capture the output of commands or the results of tasks and use them as variables in subsequent tasks. This process is known as **registering variables**. Here's how you do it:

### Example: Registering a Variable

```yaml
- name: Run a command and register the output
  shell: echo "Hello, Ansible!"
  register: command_output

- name: Display the registered variable
  debug:
    var: command_output.stdout
```

**Explanation:**
- In the first task, the `shell` module runs a command (`echo "Hello, Ansible!"`) and its output is captured and stored in a variable called `command_output`.
- The registered variable `command_output` contains several attributes, including `stdout` which holds the standard output of the command.

## 2. Variable Precedence in Ansible

In Ansible, variables can come from various sources such as inventory files, playbooks, roles, and facts. Understanding the **variable precedence** helps in determining which value a variable will take if it is defined in multiple places.

The order of precedence (from lowest to highest) is as follows:

1. **Role defaults:** Variables defined in a role's `defaults/main.yml` file.
2. **Inventory variables:** Variables set in inventory files or inventories generated dynamically.
3. **Playbook variables:** Variables defined in the playbook itself.
4. **Included files:** Variables defined in files included in the playbook.
5. **Block variables:** Variables defined within a `block`.
6. **Task facts:** Facts gathered by tasks and registered as variables.
7. **Role variables:** Variables defined in a role's `vars/main.yml` file.
8. **Set_facts:** Variables set using the `set_fact` module.
9. **Play facts:** Facts gathered during the play and available as variables.

**Note:** Variables set by higher-precedence sources override those set by lower-precedence sources.

Understanding variable precedence ensures predictable behavior in Ansible playbooks and helps avoid unexpected variable values.
