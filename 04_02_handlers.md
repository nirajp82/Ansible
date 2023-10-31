In Ansible, handlers are special tasks that are only executed when notified by other tasks. They are typically used to manage services, restart services, or perform other actions that should only be executed when a change has occurred.

Handlers are defined separately from tasks and are usually placed at the end of a playbook. When a task notifies a handler, Ansible will collect these notifications and execute the handlers after all the tasks in the current play have been processed. This ensures that handlers are triggered only when necessary, optimizing the playbook's performance.

Here's an example to illustrate how handlers work:

Let's say you have a playbook that installs a web server, copies a configuration file, and restarts the web server only if the configuration file changes.

```yaml
---
- name: Example Playbook
  hosts: web_servers
  tasks:
    - name: Install Apache web server
      ansible.builtin.package:
        name: apache2
        state: present

    - name: Copy configuration file
      ansible.builtin.copy:
        src: /path/to/config.conf
        dest: /etc/apache2/config.conf
      notify: restart apache

  handlers:
    - name: restart apache
      ansible.builtin.service:
        name: apache2
        state: restarted
```

In this example:

1. The playbook consists of two tasks: one to install Apache (`Install Apache web server`) and another to copy a configuration file (`Copy configuration file`).
2. The `notify: restart apache` line in the `Copy configuration file` task notifies the handler named `restart apache` when the configuration file is copied.
3. The handler `restart apache` uses the `service` module to restart the Apache web server (`state: restarted`) only if it is notified.

When the playbook runs:

- If Apache is not installed, the `Install Apache web server` task will install it.
- The `Copy configuration file` task will always be executed, copying the configuration file to the target server.
- If the configuration file is changed or updated, the `notify: restart apache` line will notify the `restart apache` handler.
- The handler `restart apache` will then be triggered and will restart the Apache service.

Handlers provide a way to ensure that specific actions are only taken when necessary, reducing unnecessary service restarts and improving the efficiency of your Ansible playbooks.
