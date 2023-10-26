# Ansible

### What is Ansible:
**Ansible** is an open-source automation tool used for configuration management, application deployment, task automation, and IT orchestration. It simplifies complex tasks and encourages consistent practices, improving productivity and efficiency.

### How Ansible Works:
Ansible operates by connecting to nodes (machines or servers) through SSH (Secure Shell) or Windows Remote Management (WinRM) protocols. It doesn't require agents to be installed on remote systems. Instead, it uses SSH keys or passwords for authentication and communicates with the nodes through predefined modules. These modules can perform various tasks, such as installing software, updating configurations, managing users, and more.

### Key Concepts and Components of Ansible:

1. **Playbooks:** Playbooks are written in YAML format and define a set of tasks to be executed on remote nodes. They are the core of Ansible automation, allowing users to define configurations, tasks, and roles.

2. **Roles:** Roles are a way to organize playbooks and other files in a structured format. They promote reusability and modularity in Ansible automation.

3. **Modules:** Modules are pre-built scripts that perform tasks on remote nodes. Ansible has a vast library of modules for various purposes, like managing packages, users, files, and services.

4. **Inventory:** The inventory file contains information about the managed nodes. Ansible uses this file to determine which hosts to manage and how to connect to them.

5. **Handlers:** Handlers are tasks triggered by other tasks. For instance, if a configuration file is changed, a handler can be used to restart the corresponding service.

### Why Ansible is Used:

1. **Automation:** Ansible automates repetitive tasks, saving time and reducing human error. It can handle complex tasks across multiple servers simultaneously.

2. **Configuration Management:** Ansible ensures that systems are configured in a consistent and predictable manner, facilitating easier management and troubleshooting.

3. **Application Deployment:** Ansible simplifies the deployment process by automating the setup of servers, databases, and applications, leading to faster and more reliable deployments.

4. **Collaboration:** Ansible playbooks and roles can be shared, allowing for collaboration and standardization across teams.

5. **Scalability:** Ansible can manage a large number of nodes, making it suitable for both small-scale and large-scale infrastructures.

6. **Cloud Integration:** Ansible can automate tasks in cloud environments, making it easier to manage resources and applications in platforms like AWS, Azure, and Google Cloud.

7. **Security:** Ansible promotes security by allowing secure communication (SSH encryption), version-controlled playbooks (for audit trails), and fine-grained access control.


### Example
Consider a scenario where we manage a multi-tier application consisting of web servers, application servers, and a database server. The goal is to configure each type of server appropriately using Ansible roles. We'll use roles, modules, handlers, inventory, and playbooks in this example.

### Scenario: Deploying a Multi-tier Application

#### 1. **Directory Structure:**

Create a directory structure for the Ansible project:

```
my_app/
  ├── roles/
  │   ├── webserver/
  │   │   ├── tasks/
  │   │   │   └── main.yml
  │   │   ├── templates/
  │   │   │   └── apache_virtualhost.conf.j2
  │   │   └── handlers/
  │   │       └── main.yml
  │   ├── appserver/
  │   │   └── tasks/
  │   │       └── main.yml
  │   └── dbserver/
  │       └── tasks/
  │           └── main.yml
  ├── inventory.ini
  └── main.yml
```

#### 2. **Inventory (`inventory.ini`):**

Defines the IP addresses and groups of servers.

```ini
[webservers]
web1 ansible_ssh_host=192.168.1.101
web2 ansible_ssh_host=192.168.1.102

[appservers]
app1 ansible_ssh_host=192.168.1.103

[dbservers]
db1 ansible_ssh_host=192.168.1.104
```

#### 3. **Roles:**

- **`webserver`:**
  - Installs Apache (`apache2` package) using the `apt` module.
  - Configures Apache using a Jinja2 template (`apache_virtualhost.conf.j2`).
  - Restarts Apache when the configuration changes using a handler.
   
- **`webserver/tasks/main.yml`**:

  ```yaml
  ---
  - name: Update package cache and install Apache
    apt:
      name: apache2
      state: present

  - name: Configure Apache virtual host
    template:
      src: apache_virtualhost.conf.j2
      dest: /etc/apache2/sites-available/myapp.conf
    notify: Restart Apache

  - name: Enable the virtual host
    file:
      src: /etc/apache2/sites-available/myapp.conf
      dest: /etc/apache2/sites-enabled/
      state: link
  ```

- **`webserver/templates/apache_virtualhost.conf.j2`**:

  ```apache
  <VirtualHost *:80>
      ServerAdmin webmaster@myapp.com
      ServerName myapp.com
      DocumentRoot /var/www/myapp

      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined

      <Directory /var/www/myapp>
          Options Indexes FollowSymLinks
          AllowOverride All
          Require all granted
      </Directory>
  </VirtualHost>
  ```

- **`webserver/handlers/main.yml`**:

  ```yaml
  ---
  - name: Restart Apache
    service:
      name: apache2
      state: restarted
  ```

- **`appserver`:**
  - Installs Java and Tomcat (`default-jdk` and `tomcat8` packages) using the `apt` module.

- **`appserver/tasks/main.yml`** (Example tasks for application server):

  ```yaml
  ---
  - name: Install Java and Tomcat
    apt:
      name: "{{ item }}"
      state: present
    with_items:
      - default-jdk
      - tomcat8
  ```
  
- **`dbserver`:**
  - Installs MySQL (`mysql-server` package) using the `apt` module.
  
- **`dbserver/tasks/main.yml`** (Example tasks for database server):

  ```yaml
  ---
  - name: Install MySQL
    apt:
      name: mysql-server
      state: present
  ```
  

#### 4. **Main Playbook (`main.yml`):**

The main playbook ties everything together and assigns roles to different server groups (`webservers`, `appservers`, `dbservers`). It looks like this:

```yaml
---
- name: Deploy Multi-tier Application
  hosts: webservers:appservers:dbservers
  become: yes
  roles:
    - webserver
    - appserver
    - dbserver
```

##### Explanation:

1. **Roles:**
   - The `webserver` role configures Apache on the servers defined in the `webservers` group.
   - The `appserver` role installs Java and Tomcat on servers defined in the `appservers` group.
   - The `dbserver` role installs MySQL on servers defined in the `dbservers` group.

2. **Modules:**
   - Ansible modules like `apt` (for package management) and `template` (for file templating) are used within tasks to perform actions on the servers.
   - For example, the `apt` module ensures the required packages are installed (`apache2`, `default-jdk`, `tomcat8`, `mysql-server`) on the respective servers.

3. **Handlers:**
   - The `Restart Apache` handler in the `webserver` role ensures Apache is restarted when its configuration changes, ensuring the changes take effect.

4. **Inventory:**
   - The `inventory.ini` file lists the IP addresses and groups the servers (`webservers`, `appservers`, `dbservers`) to define where each role should be applied.

5. **Playbook Execution:**
   - When you run the playbook (`ansible-playbook -i inventory.ini main.yml`), Ansible applies the roles to the corresponding server groups.
   - The roles' tasks are executed, ensuring that Apache, Java, Tomcat, and MySQL are properly installed and configured on the respective servers.

In summary, this example demonstrates how Ansible roles, modules, handlers, inventory, and playbooks work together to automate the deployment and configuration of a multi-tier application across different types of servers, promoting consistency and scalability in managing complex infrastructures.

When you run this playbook (`ansible-playbook -i inventory.ini main.yml`), Ansible will configure the web servers, application servers, and database servers according to their specific roles, utilizing modules, templates, and handlers as defined in the roles.

In summary, Ansible is a powerful automation tool used for simplifying complex IT tasks, ensuring consistency across systems, and improving overall operational efficiency. It is widely adopted in the IT industry for various automation needs.
