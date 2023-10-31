### **Understanding Ansible Roles:**

**What are Roles?**
Ansible roles are a way of organizing playbooks, making them easier to manage and reuse. A role is essentially a directory structure containing tasks, handlers, templates, and other Ansible files that collectively accomplish a specific automation task.

### **Example 1: Database Server Role**

#### Directory Structure:
```
roles/db_server/
├── tasks
│   └── main.yml
├── handlers
│   └── main.yml
└── templates
    └── my.cnf.j2
```

#### Explanation:
- **`tasks/main.yml`:**
  ```yaml
  ---
  - name: Install MySQL server
    ansible.builtin.package:
      name: mysql-server
      state: present
    notify: restart mysql
  
  - name: Copy MySQL configuration file
    ansible.builtin.template:
      src: my.cnf.j2
      dest: /etc/mysql/my.cnf
    notify: restart mysql
  ```
  This task installs MySQL server and copies a customized MySQL configuration file.

- **`handlers/main.yml`:**
  ```yaml
  ---
  - name: restart mysql
    ansible.builtin.service:
      name: mysql
      state: restarted
  ```
  This handler restarts the MySQL service when notified.

- **`templates/my.cnf.j2`:**
  ```ini
  [mysqld]
  datadir=/var/lib/mysql
  socket=/var/lib/mysql/mysql.sock
  user=mysql
  ```
  A Jinja2 template for MySQL configuration file.

#### **Usage in Playbook:**

**`webserver.yml` Playbook:**
```yaml
---
- name: Configure Database Server
  hosts: db_servers
  roles:
    - db_server
```
In this playbook:
- `db_server` role is applied to the hosts in the `db_servers` group.
- The tasks and handlers defined in the `db_server` role's directory structure will be executed on the specified hosts.

### **Example 2: Web Application Deployment Role**

#### Directory Structure:
```
roles/web_app/
├── tasks
│   └── main.yml
├── files
│   └── app.war
└── handlers
    └── main.yml
```

#### Explanation:
- **`tasks/main.yml`:**
  ```yaml
  ---
  - name: Deploy web application
    ansible.builtin.copy:
      src: app.war
      dest: /opt/tomcat/webapps/
    notify: restart tomcat
  ```
  This task copies the application file to the Tomcat webapps directory.

- **`files/app.war`:**
  A sample web application file to be deployed.

- **`handlers/main.yml`:**
  ```yaml
  ---
  - name: restart tomcat
    ansible.builtin.service:
      name: tomcat
      state: restarted
  ```
  This handler restarts the Tomcat service when notified.

#### **Usage in Playbook:**

**`webserver.yml` Playbook:**
```yaml
---
- name: Deploy Web Application
  hosts: web_servers
  roles:
    - web_app
```
In this playbook:
- `web_app` role is applied to the hosts in the `web_servers` group.
- The tasks and handlers defined in the `web_app` role's directory structure will be executed on the specified hosts.

### **Summary:**

- **Roles** in Ansible provide a structured and organized way of managing automation tasks.
- Each role contains its tasks, handlers, and other files, encapsulating specific configurations.
- Roles can be reused across different playbooks, promoting consistency and reducing redundancy in your automation code.
- Playbooks reference roles, allowing for easy and efficient management of automation tasks on specific groups of hosts.
