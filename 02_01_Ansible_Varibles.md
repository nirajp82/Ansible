**1. Variables in Ansible:**

Variables in Ansible are used to store values that can be later referenced and used in playbooks, templates, or tasks. They provide a way to make your Ansible code more dynamic and reusable. Variables can store strings, numbers, lists, dictionaries, or even complex data structures.

**2. How to Declare Variables:**

Variables can be declared in several places:

Variables in Ansible can be declared in the following places:

* **Playbook:** Variables can be declared at the top of the playbook, before any tasks are defined. These variables will be available to all tasks in the playbook.
* **Roles:** Variables can be declared in roles. Roles are reusable collections of tasks and variables that can be used in multiple playbooks. Variables declared in roles will be available to all tasks in the role.
* **Host inventory:** Variables can be declared in the host inventory. The host inventory is a file that lists all of the hosts that Ansible will manage. Variables declared in the host inventory will be available to all tasks that are executed on the host.
* **Task:** Variables can be declared within tasks. These variables will only be available to the task in which they are declared.

Here are some examples of how to declare variables in Ansible:

**Playbook:**

```yaml
---
- hosts: all
  vars:
    webserver_port: 80

  tasks:
    - name: Configure Apache2
      lineinfile:
        path: /etc/apache2/sites-available/000-default
        line: "Listen {{ webserver_port }}"
```

**Role:**

```yaml
---
---
- hosts: all
  roles:
    - webservers

  vars:
    webserver_port: 80

  tasks:
    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Configure Apache2
      lineinfile:
        path: /etc/apache2/sites-available/000-default
        line: "Listen {{ webserver_port }}"

```

**Host inventory:**

```yaml
[webservers]
example.com
webserver_port: 443
```

**Task:**

```yaml
---
- hosts: all
  tasks:
    - name: Configure Apache2
      lineinfile:
        path: /etc/apache2/sites-available/000-default
        line: "Listen {{ webserver_port }}"
      vars:
        webserver_port: 8080
```

In the above examples, the `webserver_port` variable is declared in different places. The precedence of these variables is as follows:

1. Task variables
2. Host variables
3. Role variables
4. Playbook variables

This means that the `webserver_port` variable declared in the task will override the `webserver_port` variable declared in the host inventory, role, and playbook.


**Example of Declaring Variables:**

In a playbook:

```yaml
---
vars:
  web_server_port: 80
  database_password: "secretpassword"
```

In an inventory file:

```ini
[web]
webserver ansible_host=192.168.1.101

[web:vars]
web_server_port=80
```
* [web:vars]: This line defines variables specific to the [web] group. Any variables declared under [web:vars] will be applied to all hosts within the web group. Variables provide a way to store values that can be referenced in playbooks or templates.


**3. How to Use Variables:**

Variables can be used in tasks, templates, or any other place where you need dynamic values. They are referenced using the `{{ variable_name }}` syntax.

Example of using variables in a playbook task:

```yaml
tasks:
  - name: Ensure web server is running on port 80
    service:
      name: apache2
      state: started
    vars:
      port_number: "{{ web_server_port }}"
```
In this example, the port_number variable is set to the value of web_server_port (which is 80 in this case) and used within the playbook task.

**4. Jinja2 Templating:**

Ansible uses Jinja2 templating engine to process variables and templates. Jinja2 allows you to use control structures like loops and conditionals, making your templates dynamic.

**Example:**

Consider the template file `index.html.j2` with Jinja2 templating:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Web Page</title>
</head>
<body>
  <h1>Welcome to my website!</h1>
  {% if show_footer %}
    <footer>
      &copy; {{ current_year }}
    </footer>
  {% endif %}
</body>
</html>
```

Explanation:

- **`{% if show_footer %}`:** This line is a Jinja2 conditional statement. It checks if the variable `show_footer` is truthy (i.e., not empty, zero, `false`, or `None`). If `show_footer` is true, it includes the content inside the `if` block in the final rendered template.

- **`&copy; {{ current_year }}`:** This line includes a variable `current_year`. The double curly braces `{{ ... }}` denote Jinja2 variables. Ansible would replace `{{ current_year }}` with the actual value of the variable when rendering the template. For example, if `current_year` is defined as `2023`, the rendered output would be `&copy; 2023`.

- **`{% endif %}`:** This line marks the end of the `if` block. Any content after this line will be included in the final template regardless of the `show_footer` variable's value, because it's outside the conditional block.

**Usage in Ansible Playbook:**

In your Ansible playbook, you could have a task like this:

```yaml
- name: Render Web Page
  template:
    src: index.html.j2
    dest: /path/to/output/index.html
  vars:
    show_footer: true
    current_year: 2023
```

In this task:

- `show_footer: true` sets the `show_footer` variable to `true`, allowing the content inside the `if` block in the template to be included in the final output.
- `current_year: 2023` sets the `current_year` variable, which is used in the template to display the current year.

When the playbook runs, it uses these variables to render the `index.html.j2` template, creating the final HTML file with or without the footer based on the `show_footer` variable's value.

By using variables and Jinja2 templating, Ansible allows you to create flexible, dynamic, and reusable automation solutions.

**4. [all:vars]:**
In Ansible, `[all:vars]` is a section in an inventory file that allows you to define variables that apply to all hosts listed in the inventory. Variables defined under `[all:vars]` are global and will be available for all hosts, groups, and plays specified in the inventory file.

For example, consider the following inventory file:

```ini
[all:vars]
ansible_user=admin
ansible_ssh_private_key_file=/path/to/private_key.pem

[web_servers]
web1 ansible_host=192.168.1.1
web2 ansible_host=192.168.1.2
```

In this example, `[all:vars]` is used to define two variables: `ansible_user` and `ansible_ssh_private_key_file`. These variables are applicable to all hosts in the inventory. So, any tasks or plays that run on `web1` and `web2` will use the `admin` user and the specified SSH private key file.

By defining variables in `[all:vars]`, you can avoid duplicating variable definitions for each host or group in your inventory file, making it more manageable and efficient, especially when you have a large number of hosts that share common configuration settings.
