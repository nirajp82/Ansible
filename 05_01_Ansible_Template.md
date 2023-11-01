**Ansible Templates** allow you to create dynamic configuration files by combining static text with data from variables. You can perform various operations like string manipulation and use filters to format and manipulate the data in your templates.

Here's an example that demonstrates the use of Ansible templates with string manipulation, list iteration, and set filters:

### **Example: Generating an Nginx Configuration File**

Suppose you want to generate an Nginx configuration file with a list of server names and perform string manipulation to add custom headers to each server block.

#### **Playbook: `nginx_config.yml`**

```yaml
---
- name: Generate Nginx Configuration File
  hosts: localhost
  gather_facts: no
  vars:
    nginx_servers:
      - server_name: example.com
        custom_headers:
          - 'X-Frame-Options "SAMEORIGIN";'
          - 'X-Content-Type-Options "nosniff";'
      - server_name: another-example.com
        custom_headers:
          - 'Strict-Transport-Security "max-age=31536000; includeSubdomains";'
  tasks:
    - name: Generate Nginx Configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/nginx.conf
      become: yes
```

In this playbook:

- We define a list of `nginx_servers` with server names and custom headers.
- We use the `template` module to render the `nginx.conf.j2` Jinja2 template and save it as `/etc/nginx/sites-available/nginx.conf`.

#### **Jinja2 Template: `nginx.conf.j2`**

```jinja2
{% for server in nginx_servers %}
server {
    listen 80;
    server_name {{ server.server_name }};

    {% for header in server.custom_headers %}
    add_header {{ header }};
    {% endfor %}
}
{% endfor %}
```

In this template:

- We use a `for` loop to iterate through the list of `nginx_servers`.
- For each server, we create an Nginx server block.
- Within the server block, we use another `for` loop to iterate through the custom headers and add them using the `add_header` directive.

#### **Running the Playbook:**

Execute the playbook using the following command:

```bash
ansible-playbook nginx_config.yml
```

This playbook will generate an Nginx configuration file at `/etc/nginx/sites-available/nginx.conf` with server blocks for `example.com` and `another-example.com`, each containing the custom headers specified in the variables.

By using Ansible templates with string manipulation, list iteration, and set filters, you can dynamically generate configuration files tailored to your specific requirements and automate the configuration of various services.
