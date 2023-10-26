**1. Variables in Ansible:**

Variables in Ansible are used to store values that can be later referenced and used in playbooks, templates, or tasks. They provide a way to make your Ansible code more dynamic and reusable. Variables can store strings, numbers, lists, dictionaries, or even complex data structures.

**2. How to Declare Variables:**

Variables can be declared in several places:

- **Inventory Files:** Variables can be associated with hosts or groups in inventory files.
- **Playbooks:** Variables can be defined directly in playbooks.
- **Roles:** Variables can be defined in role defaults, vars files, or task files.
- **External Sources:** Variables can also come from external sources, like command outputs or files read in tasks.

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
