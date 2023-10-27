## Ansible Variables: Registering Variables and Variable Precedence

### Table of Contents

1. **Registering Variables and Accessing Their Values**
2. **Variable Precedence in Ansible**

---

## 1. Registering Variables and Accessing Their Values

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
Ansible variable precedence determines the order in which Ansible resolves variable values. When Ansible encounters a variable, it will search for the variable in each variable scope in turn, starting with the most specific scope and ending with the most general scope. If the variable is found in a more specific scope, it will be used in preference to a variable with the same name in a less specific scope.

The Ansible variable precedence is as follows:

1. **Task variables:** Variables defined in the current task.
2. **Block variables:** Variables defined in the current block.
3. **Role variables:** Variables defined in the role that is being executed.
4. **Play variables:** Variables defined in the playbook.
5. **Host variables:** Variables defined for the current host in the inventory.
6. **Facts:** Facts gathered from the current host.
7. **Global variables:** Variables defined in the Ansible configuration file, environment variables, and command line arguments.

   Task variables have the highest precedence, followed by block variables, role variables, play variables, host variables, facts, and global variables.

This means that a variable defined in the current task will override a variable with the same name defined in the block, role, play, host, fact, or global scope.

It is important to note that variable precedence is hierarchical, meaning that variables in more specific scopes have higher precedence than variables in less specific scopes. This means that a variable defined in the current task will override a variable with the same name defined in the playbook.

Here is an example of how variable precedence works in Ansible:

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

In this example, the `webserver_port` variable is defined in both the play scope and the host scope. However, the host scope has higher precedence than the play scope. This means that the value of the `webserver_port` variable will be 443, which is the value defined in the host scope.

You can also use variable precedence to override the default behavior of Ansible. For example, you can use the `hostvars` scope to override the value of a global variable for a specific host. This can be useful for configuring hosts with different settings.

Here is an example of how to use the `hostvars` scope to override the value of a global variable:

```yaml
# Global variables
ansible_python_interpreter: /usr/bin/python2

# Host variables
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


By understanding how variable precedence works in Ansible, you can write more efficient and reusable playbooks.

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
