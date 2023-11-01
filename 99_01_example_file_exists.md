```yaml
- name: Conditional Rendering Example
  hosts: localhost
  vars:
    file_path: "/path/to/your/file"  # Specify the path to the file you want to check
  tasks:
    - name: Check if file exists
      stat:
        path: "{{ file_path }}"
      register: file_info

    - name: Render HTML based on file existence
      template:
        src: template.j2
        dest: /path/to/output/file.html
      vars:
        condition: "{{ file_info.stat.exists }}"
```

- `register: file_info`: This line of code registers the output of the `stat` module to a variable named `file_info`. When a task is executed, Ansible collects information from the task and stores it in the variable specified after `register:`. In this case, the `stat` module checks whether the file specified by the `file_path` variable exists, and the resulting information is stored in the `file_info` variable.

- `stat`: This is a Ansible module that provides access to file attributes on remote systems. In this case, it checks if the file specified in the `path` parameter (which is `{{ file_path }}` in this playbook) exists or not. The `stat` module collects various attributes of the file, such as size, modification time, and whether it's a directory or a regular file.

- `path: "{{ file_path }}"`: This is a parameter of the `stat` module. `{{ file_path }}` is a Jinja2 template variable that gets replaced with the value of the `file_path` variable defined in the `vars` section of the playbook. It specifies the path to the file that the `stat` module will check for existence.

- `condition: "{{ file_info.stat.exists }}"`: After registering the output of the `stat` module in the `file_info` variable, the `Render HTML based on file existence` task uses this variable. The `file_info.stat.exists` expression evaluates to `true` if the file specified in `file_path` exists and `false` otherwise. This condition is then used in your template (`template.j2`) or other parts of your playbook logic to determine the behavior based on the existence of the file. For example, in your template, you can use this `condition` variable to conditionally render HTML content based on whether the file exists or not.
