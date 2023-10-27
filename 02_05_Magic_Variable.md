## Ansible Magic Variables

Magic variables in Ansible are special variables that are set by Ansible itself and cannot be overridden by users. They provide access to information about the current playbook run, such as the host being operated on, the groups the host belongs to, and the Ansible facts that have been gathered.

**Why use magic variables?**

Magic variables can be used to make your Ansible playbooks more dynamic and reusable. For example, you can use magic variables to:

* Access the IP address of the current host
* Determine whether the current host has a specific package installed
* Target specific groups of hosts with a task
* Use the behavior or state of one system as configuration on another system

**Common magic variables**

Here are some of the most commonly used magic variables in Ansible:


1. **`ansible_date_time`**:
   - Contains information about the current date and time on the Ansible control node.
   
2. **`ansible_default_ipv4`** and **`ansible_default_ipv6`**:
   - Contain information about the default IPv4 and IPv6 addresses of the target host respectively.

3. **`ansible_distribution`**, **`ansible_distribution_version`**, **`ansible_os_family`**:
   - Provide details about the Linux distribution, its version, and the OS family (like Debian, RedHat, etc.) of the target system.

4. **`ansible_env`**:
   - Contains environment variables of the Ansible controller system.

5. **`ansible_facts`**:
   - Contains all facts gathered from the target host.

6. **`ansible_hostname`**:
   - Stores the hostname of the target system.

7. **`ansible_inventory_hostname`**:
   - Contains the hostname of the target system as provided in the inventory file.

8. **`ansible_play_batch`** and **`ansible_play_hosts`**:
   - Contain a list of all hosts in the current play batch or all hosts in the current play, respectively.

9. **`ansible_python_version`**:
   - Contains the version of Python installed on the target system.

10. **`ansible_ssh_host`** and **`ansible_ssh_port`**:
    - Store the SSH host and port information used to connect to the target host.

11. **`ansible_user`** and **`ansible_ssh_user`**:
    - Store the user used to SSH into the target host.

12. **`inventory_hostname`**:
    - Contains the hostname of the current host being iterated in a play.

13. **`group_names`** and **`groups`**:
    - `group_names` contains a list of groups the current host is a member of, while `groups` Groups return all hosts under a given group.

**How to use magic variables**

To use a magic variable in an Ansible playbook or task, simply surround the variable name with double curly braces (`{{` and `}}`). For example, to access the `ansible_host` variable of the current host, you would use the following expression:

```
{{ ansible_host }}
```

You can also use magic variables in combination with other Ansible features, such as templates and Jinja2 filters. For example, the following template uses the `hostvars` magic variable to access the `ansible_host` variable of the current host and the `groups` magic variable to access the list of groups that the current host belongs to:

```
Hostname: {{ hostvars['inventory_hostname'] }}
Groups: {{ groups | join(', ') }}
```

**Examples**

Here are some examples of how to use magic variables in Ansible playbooks:

```yaml
# Get the IP address of the current host
- name: Get IP address
  debug:
    var: "{{ ansible_host }}"

# Check if a specific package is installed on the current host
- name: Check if package is installed
  debug:
    var: "{{ ansible_facts.packages.contains('apache2') }}"

# Target all hosts in the 'webservers' group with a task
- name: Install Apache2 on web servers
  apt:
    name: apache2
  groups: webservers

# Use the IP address of another host as a configuration value
- name: Configure remote database connection
  lineinfile:
    path: /etc/my.cnf
    line: "host={{ hostvars['database.example.com']['ansible_host'] }}"

# Use the behavior or state of one system as configuration on another system
- name: Configure firewall to allow inbound traffic from all web servers
  firewalld:
    port: 80/tcp
    permanent: yes
    state: enabled
    zone: public
    source: "{{ groups['webservers'] | join(',') }}"
```

**Conclusion**

Magic variables are a powerful tool that can help you write more efficient and reusable Ansible playbooks. By understanding how to use magic variables, you can make your playbooks more dynamic and adaptable to different environments.

**Additional information**

* For a complete list of magic variables, see the Ansible documentation: https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html.
* You can also use magic variables in Ansible roles.
* Magic variables can be used to create more complex and dynamic Ansible playbooks and roles.
* With a good understanding of magic variables, you can write Ansible playbooks that can be used to manage a wide variety of systems and applications.
