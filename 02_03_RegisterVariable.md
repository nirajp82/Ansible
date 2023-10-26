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


## Understanding Variable Precedence in Ansible

Ansible applies variable precedence, which determines the order in which variables are chosen when multiple options exist. Here's the precedence order from least to greatest (variables listed last override all others):

- Command line values (e.g., -u my_user, not considered variables)
- Role defaults (defined in role/defaults/main.yml)
- Inventory file or script group vars
- Inventory group_vars/all
- Playbook group_vars/all
- Inventory group_vars/*
- Playbook group_vars/*
- Inventory file or script host vars
- Inventory host_vars/*
- Playbook host_vars/*
- Host facts / cached set_facts
- Play vars
- Play vars_prompt
- Play vars_files
- Role vars (defined in role/vars/main.yml)
- Block vars (only for tasks in block)
- Task vars (only for the task)
- include_vars
- set_facts / registered vars
- Role (and include_role) params
- Include params
- Extra vars (e.g., -e "user=my_user") always win precedence

In general, Ansible prioritizes variables defined more recently, actively, and with explicit scope. Variables in a role's defaults folder are easily overridden. Variables in the vars directory of a role override previous versions in the namespace. Host and/or inventory variables override role defaults, but explicit includes like vars directory or include_vars tasks take precedence over inventory variables.

Ansible merges variables set in inventory, allowing more specific settings to override generic ones. For example, ansible_ssh_user specified as a group_var is overridden by ansible_user specified as a host_var. For details about variable precedence in inventory, see [How variables are merged](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#how-variables-are-merged).

References: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable
