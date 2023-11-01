To create a basic Ansible project structure with directories for templates, tasks, and playbooks, you can use the following commands. This structure provides a clean organization for your Ansible projects:

```bash
mkdir my_ansible_project
cd my_ansible_project

# Create directories for tasks, templates, and playbooks
mkdir tasks
mkdir templates
mkdir playbooks

# Create an empty playbook file inside the playbooks directory
touch playbooks/site.yml

# Create a sample task file inside the tasks directory
touch tasks/main.yml
```

Explanation of the created directories and files:

- `my_ansible_project/`: The main directory for your Ansible project.
- `tasks/`: Directory where you can store your task files.
- `templates/`: Directory for Jinja2 template files (if you're using templates in your tasks/playbooks).
- `playbooks/`: Directory to store your playbook files.
- `playbooks/site.yml`: An example playbook file. You can create additional playbook files as needed.
- `tasks/main.yml`: An example task file. You can create more task files based on your requirements and include them in your playbooks.

Feel free to modify the structure and add more directories/files based on your specific project needs.

 Here's an example of an Ansible project structure using the directories you created (`tasks`, `templates`, and `playbooks`) and how you can create a playbook that utilizes tasks and templates:

### Directory Structure:
```plaintext
my_ansible_project/
|-- tasks/
|   |-- main.yml
|-- templates/
|   |-- sample_template.j2
|-- playbooks/
|   |-- site.yml
```

### `my_ansible_project/tasks/main.yml`:
```yaml
---
- name: Ensure required directories exist
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Create tasks directory
      file:
        path: /path/to/my_ansible_project/tasks
        state: directory

    - name: Create templates directory
      file:
        path: /path/to/my_ansible_project/templates
        state: directory

    - name: Create playbooks directory
      file:
        path: /path/to/my_ansible_project/playbooks
        state: directory
```

### `my_ansible_project/templates/sample_template.j2`:
```jinja2
Hello, this is a sample template file.
```

### `my_ansible_project/playbooks/site.yml`:
```yaml
---
- name: Run tasks and template example
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Include tasks from the tasks directory
      include_tasks: ../tasks/main.yml

    - name: Create a file from a template
      template:
        src: ../templates/sample_template.j2
        dest: /path/to/my_ansible_project/output.txt
```

In this setup:

- The `tasks/main.yml` file ensures that the required directories exist.
- The `templates/sample_template.j2` file is a simple Jinja2 template.
- The `playbooks/site.yml` playbook includes tasks from the `tasks/main.yml` file and creates a file (`output.txt`) by rendering the template.

Make sure to replace `/path/to/my_ansible_project` with the actual path to your Ansible project directory. When you run the `site.yml` playbook, it will create the necessary directories and a file with the content from the template.
