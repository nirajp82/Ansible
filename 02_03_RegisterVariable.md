## Ansible Variables: Registering Variables and Variable Precedence

### Table of Contents

1. **Registering Variables and Accessing Their Values**
2. **Variable Precedence in Ansible**

---

## 1. Registering Variables and Accessing Their Values

Registering Variables and Accessing Their Values
**Registering Variables**

Registering variables in Ansible allows you to store the output of a task or the results of a loop in a variable. This can be useful for a variety of purposes, such as:

* Using the output of one task as input to another task
* Storing the results of a loop for later use
* Creating conditional statements based on the value of a registered variable

To register a variable in Ansible, you use the `register` keyword. For example, the following task will register the output of the `uptime` command in a variable named `my_uptime`:

```
- name: Get uptime
  command: uptime
  register: my_uptime
```

You can also register the results of a loop using the `register` keyword. For example, the following task will register the list of all installed packages in a variable named `my_packages`:

```
- name: Get installed packages
  dpkg_info:
  register: my_packages
```

**Accessing the Values of Registered Variables**

To access the value of a registered variable, you use the `{{` and `}}` curly braces. For example, the following task will print the value of the `my_uptime` variable to the console:

```
- name: Print uptime
  debug:
    msg: "{{ my_uptime.stdout }}"
```

You can also use registered variables in conditional statements. For example, the following task will only execute if the `my_uptime.stdout` variable contains the word "up":

```
- name: If uptime is up, then...
  debug:
    msg: "Uptime is up!"
  when: "{{ my_uptime.stdout.contains('up') }}"
```

**Examples**

Here are some more examples of how to use registered variables in Ansible:

```yaml
# Get the IP address of the current host and store it in a variable
- name: Get IP address
  command: ifconfig eth0 | grep "inet addr:" | awk '{print $2}'
  register: my_ip_address

# Use the IP address of the current host to configure a firewall rule
- name: Configure firewall rule
  firewalld:
    port: 80/tcp
    permanent: yes
    state: enabled
    zone: public
    source: "{{ my_ip_address.stdout }}"

# Get the list of all installed packages and store it in a variable
- name: Get installed packages
  dpkg_info:
  register: my_packages

# Loop through the list of installed packages and uninstall any packages that are not needed
- name: Uninstall unused packages
  apt:
    name: "{{ item }}"
    state: absent
  loop: "{{ my_packages.results }}"
  when: not item.installed
```

**Conclusion**

Registering variables is a powerful feature of Ansible that allows you to store the output of tasks and loops for later use. By understanding how to register variables and access their values, you can write more efficient and reusable Ansible playbooks.


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
