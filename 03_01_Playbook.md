# Ansible Playbooks: Overview and Structure

## Overview:
Ansible playbooks are configuration files written in YAML format that define a set of tasks to be executed on remote hosts. Playbooks serve as the building blocks of Ansible automation, allowing you to define complex tasks, configurations, and deployments in a human-readable format. They enable you to automate repetitive tasks, manage configurations, and orchestrate complex workflows across multiple servers.

## Structure of an Ansible Playbook:

### 1. **Hosts/Inventory Section:**
   Specifies the target hosts or groups on which the playbook tasks will be executed. It can be a list of hostnames or IP addresses or reference an inventory file.
   ```yaml
   ---
   - hosts: web_servers
   ```

### 2. **Variables Section:**
   Defines playbook-level variables. These can be used throughout the playbook for dynamic configurations.
   ```yaml
   vars:
     app_port: 8080
   ```

### 3. **Tasks Section:**
   Contains a list of tasks to be executed on the specified hosts. Each task typically represents a single action or a set of related actions.
   ```yaml
   tasks:
     - name: Ensure Apache is installed
       apt:
         name: apache2
         state: present
       become: yes

     - name: Start Apache Service
       service:
         name: apache2
         state: started
   ```

### 4. **Handlers Section (Optional):**
   Handlers are special tasks triggered by other tasks. They are used for tasks like service restarts triggered when configuration files change.
   ```yaml
   handlers:
     - name: Restart Apache
       service:
         name: apache2
         state: restarted
   ```

### 5. **Roles (Optional):**
   Roles allow you to organize your playbook into reusable and shareable units. They contain tasks, variables, and files related to a specific functionality.
   ```yaml
   roles:
     - web_server
   ```

### 6. **Includes and Imports (Optional):**
   Playbooks can include or import other YAML files containing additional tasks and configurations, making playbooks modular and easier to maintain.
   ```yaml
   - import_playbook: common_tasks.yml
   ```

## Execution:
To execute a playbook, you can use the `ansible-playbook` command followed by the playbook filename.
```sh
ansible-playbook playbook.yml
```

Ansible playbooks provide a powerful way to automate infrastructure management and application deployment. By following this structured format, you can create efficient, organized, and maintainable automation solutions for your IT environment.


![image](https://github.com/nirajp82/Ansible/assets/61636643/1c6af829-65ac-4219-91d3-6e57962a9b61)

# Ansible Play vs Ansible Playbook

In Ansible, understanding the difference between ad hoc commands, plays, and playbooks is essential for effective automation. Here's a breakdown:

## Ansible Ad Hoc Commands:
Ad hoc commands are single, simple tasks executed against targeted hosts. They are ideal for one-time tasks. However, the true power of Ansible lies in playbooks.

## Ansible Play:
A play is an ordered set of tasks that should be executed against hosts selected from your inventory. Each play specifies:

- **Hosts:** The targeted hosts or host groups from the inventory.
- **Tasks:** A list of actions to be performed on the specified hosts.

Think of a play as a connection between hosts and tasks. For instance, in the given example, tasks are run against the `webservers` host group. This setup constitutes a play.

## Ansible Playbook:
A playbook is a YAML text file containing one or more plays to execute in order. Playbooks provide a structured and repeatable way to automate tasks. They allow you to define multiple plays, each designed for different host groups.

For instance, if you need to run distinct tasks against different host groups, you can achieve this by adding multiple plays within a playbook. Playbooks are powerful tools for orchestrating complex configurations and deployments.

Remember:
- **Play:** Connects hosts to tasks, executed in order within a playbook.
- **Playbook:** Contains multiple plays, enabling automation across diverse host groups.

Understanding these concepts empowers you to create organized, reusable, and efficient automation solutions in Ansible. Playbooks provide a structured approach to managing your infrastructure, making automation scalable and manageable.

** Example Ansible Playbook with multiple hosts and multiple plays. **
Here is the ansible playbook with multiple hosts in it.  You can see we are working with web and application servers in the same playbook and executing two different plays (set of tasks) respectively.

```yaml
 # Play1 - WebServer related tasks
  - name: Play Web - Create apache directories and username in web servers
    hosts: webservers
    become: yes
    become_user: root
    tasks:
      - name: create username apacheadm
        user:
          name: apacheadm
          group: users,admin
          shell: /bin/bash
          home: /home/weblogic

      - name: install httpd
        yum:
          name: httpd
          state: installed
        
  # Play2 - Application Server related tasks
  - name: Play app - Create tomcat directories and username in app servers
    hosts: appservers
    become: yes
    become_user: root
    tasks:
      - name: Create a username for tomcat
        user:
          name: tomcatadm
          group: users
          shell: /bin/bash
          home: /home/tomcat

      - name: create a directory for apache tomcat
        file:
          path: /opt/oracle
          owner: tomcatadm
          group: users
          state: present
          mode: 0755
```
In the preceding playbook, you could notice that two plays are there and both of them targeted to different host groups.

How to Execute an Ansible Playbook


References:
https://www.middlewareinventory.com/blog/ansible-playbook-example/
