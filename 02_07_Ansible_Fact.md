**Ansible Facts:**

Ansible facts are pieces of information about remote systems that Ansible gathers automatically during the playbook execution. These facts include details about the system's hardware, operating system, network interfaces, and other characteristics. Ansible uses these facts to make intelligent decisions during playbook execution, allowing for dynamic and adaptable automation.

**How Ansible Gathers Facts:**

When an Ansible playbook runs, it automatically collects facts from the target hosts. This process involves executing small scripts, called "gather facts modules," on the remote systems. These modules are part of Ansible's standard library. They collect information and store it in variables, making it accessible for use within the playbook.

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
    - name: Gather Facts
      setup:  # The setup module gathers facts about the target host

    - name: Install Package
      package:
        name: "{{ 'apache2' if ansible_facts['ansible_distribution'] == 'Ubuntu' else 'httpd' }}"
        state: present
      become: yes
```

In this example, Ansible gathers facts about the target host's distribution using the `setup` module. Based on the distribution (Ubuntu or others), it decides which package manager (`apt` or `yum`) to use for installing the `apache2` or `httpd` package. The playbook adapts its actions dynamically based on the facts collected from the remote system.
