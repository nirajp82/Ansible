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


