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

When an Ansible playbook runs, it automatically collects facts from the target hosts. This process involves executing small scripts, called "gather facts modules" ( the default is to use the ansible.builtin.setup module.) on the remote systems as part of the setup process. These modules are part of Ansible's standard library. They collect information and store it in variables, making it accessible for use within the playbook.

**Note** - In Ansible, facts gathering is limited to the hosts explicitly mentioned in the playbook's hosts directive. Ansible will only collect facts for the hosts mentioned in the specified playbook. This module is automatically run during a play’s fact gathering stage to gather useful variables about remote hosts that can be used in playbooks.

Suppose your /etc/ansible/hosts file contains two servers, web1 and web2. If your playbook specifies only web1 as the target host:
-- /etc/ansible/hosts
```
web1
web2
```

```yml
- hosts: web1
  tasks:
    - name: Task 1
      debug:
        msg: "This task runs on web1 only"
```
In this case, Ansible will collect facts only for the web1 host. Even though web2 is mentioned in the inventory file, its facts won't be gathered because it's not referenced in the playbook. This selective gathering of facts allows you to optimize the information collection process, especially when dealing with large inventories.

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

### Caching Facts

Facts in Ansible are pieces of information about remote systems. By default, Ansible gathers these facts and stores them in memory for the duration of the playbook run. However, facts can also be cached independently for repeated use, allowing you to reference facts from one system when configuring another, even if Ansible processes the play on the second system first. For instance:

```yaml
{{ hostvars['asdf.example.com']['ansible_facts']['os_family'] }}
```

Fact caching is controlled by cache plugins. Ansible's default behavior uses the memory cache plugin, retaining facts in memory during the playbook run. To persist Ansible facts for reuse, you can choose an alternative cache plugin. Refer to the [Cache plugins documentation](https://docs.ansible.com/ansible/latest/plugins/cache.html) for more information.

Utilizing fact caching can significantly enhance performance. For large-scale infrastructures, you can configure fact caching to run periodically, allowing you to manage configuration on a smaller subset of servers throughout the day. This approach provides access to variables and information about all hosts, even when managing only a few servers.

### Disabling Facts

By default, Ansible gathers facts at the beginning of each play. However, there might be situations where you don't need to gather facts, especially if you already possess comprehensive information about your systems centrally. Disabling fact gathering at the play level can significantly improve scalability, particularly in push mode with a vast number of systems, or when using Ansible on experimental platforms.

To disable fact gathering, use the following syntax in your playbook:

```yaml
- hosts: whatever
  gather_facts: false
```
