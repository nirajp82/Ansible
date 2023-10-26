## Ansible Variables: Registering Variables and Variable Precedence

### Table of Contents

1. **Registering Variables and Accessing Their Values**
2. **Variable Precedence in Ansible**

---

## 1. Registering Variables and Accessing Their Values

When you use the `register` statement in an Ansible task, it captures the output of a command or the result of a task and stores it in a variable for later use. Here's the breakdown of the statement:

```yaml
- name: Run a command and register the output
  shell: echo "Hello, Ansible!"
  register: command_output
```

In this example:
- `shell: echo "Hello, Ansible!"` runs the shell command and produces the output `"Hello, Ansible!"`.
- `register: command_output` captures this output and stores it in a variable called `command_output`.

Now, let's understand the relation with `var: command_output.stdout`.

```yaml
- name: Display the registered variable
  debug:
    var: command_output.stdout
```

In this part:
- `debug` is a Ansible module used to print messages during playbook execution.
- `var: command_output.stdout` accesses the `stdout` attribute of the variable `command_output`.

Here's a step-by-step explanation:

1. **Running the Command:**
   - The `shell` module executes the command `echo "Hello, Ansible!"`.
   - The output, `"Hello, Ansible!"`, is generated.

2. **Registering the Output:**
   - `register: command_output` captures the output and saves it in the variable `command_output`.

3. **Accessing the Output:**
   - `command_output.stdout` refers to the standard output of the registered command.

When you run this YAML file using Ansible, it executes the shell command, captures the output, and then prints it using the `debug` module.

### Example of Running the YAML File:

Save the above YAML code in a file named `example.yml`. To execute the playbook, use the `ansible-playbook` command:

```bash
ansible-playbook example.yml
```

This command will run the playbook, displaying `"Hello, Ansible!"` as the output because it's captured by `command_output` and accessed through `command_output.stdout` in the `debug` module.

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
