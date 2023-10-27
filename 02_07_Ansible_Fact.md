**Ansible Facts:**

Ansible facts are data related to your remote systems, including operating systems, IP addresses, attached filesystems, and more. You can access this data in the ansible_facts variable. By default, you can also access some Ansible facts as top-level variables with the ansible_ prefix. You can disable this behavior using the INJECT_FACTS_AS_VARS setting. 

```yaml
- name: Print all available facts
  ansible.builtin.debug:
    var: ansible_facts
```

To see the ‘raw’ information as gathered, run this command at the command line:

```ansible <hostname> -m ansible.builtin.setup```

**How Ansible Gathers Facts:**

When an Ansible playbook runs, it automatically collects facts from the target hosts. This process involves executing small scripts, called "gather facts modules" ("setup module") on the remote systems as part of the setup process. These modules are part of Ansible's standard library. They collect information and store it in variables, making it accessible for use within the playbook.

**Flow of Gathering Facts:**

1. **Playbook Execution Begins:** When you run an Ansible playbook, the first step involves connecting to the target hosts specified in the inventory file.

2. **Gather Facts Modules:** Ansible automatically selects appropriate "gather facts modules" based on the target host's operating system. For example, on a Linux system, it might use the `setup` module to gather facts.

3. **Execution on Remote Host:** Ansible sends the gather facts module to the remote host, where it is executed.

4. **Facts Collection:** The module collects system information and sends it back to the Ansible control node.

5. **Facts Stored:** The collected facts are stored in the `ansible_facts` variable. These facts can be used in subsequent tasks and plays within the playbook.

**Example:**

Consider a playbook that needs to install a package based on the Linux distribution running on the target host. Ansible facts can be used to determine the appropriate package manager.

```yaml
---
- name: Gather Facts Example
  hosts: web_servers
  tasks:
    - name: Install Package
      package:
        name: "{{ 'apache2' if ansible_facts['ansible_distribution'] == 'Ubuntu' else 'httpd' }}"
        state: present
      become: yes
```

In this example, Ansible gathers facts about the target host's distribution using the `setup` module. Based on the distribution (Ubuntu or others), it decides which package manager (`apt` or `yum`) to use for installing the `apache2` or `httpd` package. The playbook adapts its actions dynamically based on the facts collected from the remote system.
