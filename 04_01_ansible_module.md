Ansible modules are the basic building blocks of Ansible playbooks. They are self-contained units of functionality that can be used to perform a variety of tasks on remote hosts, such as installing packages, configuring services, and managing files.

Ansible modules are categorized into the following groups:

* **Core modules:** These modules are shipped with Ansible and provide basic functionality, such as managing files, users, and packages.
* **Collections:** Collections are community-developed modules that provide additional functionality, such as managing cloud resources, databases, and networking devices.
* **Custom modules:** You can also write your own custom modules to meet your specific needs.

Here is a table of some of the most popular Ansible modules, categorized by group:

| Group | Module | Description |
|---|---|---|
| Core | file | Manages files and directories on remote hosts. |
| Core | package | Manages packages on remote hosts. |
| Core | user | Manages users and groups on remote hosts. |
| Core | service | Manages services on remote hosts. |
| Collections | aws_instance | Manages Amazon Web Services (AWS) EC2 instances. |
| Collections | mysql_user | Manages MySQL users and databases. |
| Collections | cisco_ios_config | Configures Cisco IOS devices. |
| Custom | my_custom_module | A custom module that you have written. |

To use an Ansible module, you simply need to include it in your playbook task. For example, the following task will install the Apache package on all remote hosts:

```yaml
- hosts: all
  tasks:
  - name: Install Apache
    yum:
      name: httpd
      state: present
```

The `yum` module is a core Ansible module that is used to manage packages on Red Hat-based systems. The `name` argument specifies the package to install, and the `state` argument specifies the desired state of the package.

Ansible modules can also be used to perform more complex tasks. For example, the following task will create a new user account on all remote hosts and then grant the user sudo privileges:

```yaml
- hosts: all
  tasks:
  - name: Create a user account
    user:
      name: my_user
      state: present

  - name: Grant sudo privileges to the user
    lineinfile:
      path: /etc/sudoers
      line: my_user ALL=(ALL) ALL
      state: present
```

The `user` module is a core Ansible module that is used to manage users and groups on remote hosts. The `state` argument specifies the desired state of the user account.

The `lineinfile` module is a core Ansible module that is used to manage lines in files. The `path` argument specifies the path to the file to modify, the `line` argument specifies the line to add, and the `state` argument specifies the desired state of the line.

Ansible modules are a powerful tool that can be used to automate a wide variety of tasks. By using modules, you can save time and reduce the risk of errors.

