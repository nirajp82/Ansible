# Ansible Variable Scopes
Ansible variable scopes determine where a variable is defined and how it can be accessed. Ansible has three main variable scopes:

* **Global:** This scope is the most general and includes all variables defined in the Ansible configuration file, environment variables, and command line arguments.

 ![image](https://github.com/nirajp82/Ansible/assets/61636643/9564ec1a-7574-476f-b6a9-a480a1569212)
  
* **Play:** This scope includes all variables defined in the play itself, as well as variables defined in any roles that are included in the play.

![image](https://github.com/nirajp82/Ansible/assets/61636643/6bbfa28b-5dab-4178-9669-ab4d22532b37)

* **Host:** This scope includes all variables defined for the specific host that is being operated on. This includes variables defined in the inventory, facts gathered from the host, and variables registered by previous tasks.
![image](https://github.com/nirajp82/Ansible/assets/61636643/1cad725c-b464-4dee-9f55-26caed46b9b3)

Variable scopes are hierarchical, with global variables having the lowest precedence and host variables having the highest precedence. This means that a variable defined in the host scope will override a variable defined in the play scope, which will override a variable defined in the global scope.

In addition to the three main variable scopes, Ansible also has a number of special scopes, such as the `hostvars` and `facts` scopes. These scopes provide access to variables that are specific to the current host or to the Ansible facts that have been gathered for the current host.

Here is an example of how variable scopes work in Ansible:

```yaml
# Global variables
ansible_python_interpreter: /usr/bin/python3

# Play variables
webserver_port: 80

# Host variables
webserver_host: example.com

# Tasks
- name: Install Apache2
  apt:
    name: apache2
  register: apache2_installed

- name: Configure Apache2
  lineinfile:
    path: /etc/apache2/sites-available/000-default
    line: "Listen {{ webserver_port }}"
  when: apache2_installed.status == 0
```

In this example, the `ansible_python_interpreter` variable is defined in the global scope and will be available to all tasks in the playbook. The `webserver_port` variable is defined in the play scope and will be available to all tasks in the play, except for the `Install Apache2` task, which is defined in a role. The `webserver_host` variable is defined in the host scope and will be available to the `Configure Apache2` task, which is executed on the host `example.com`.

You can also use variable scopes to override the default behavior of Ansible. For example, you can use the `hostvars` scope to override the value of a global variable for a specific host. This can be useful for configuring hosts with different settings.

Here is an example of how to use the `hostvars` scope to override the value of a global variable:

```yaml
# Global variables
ansible_python_interpreter: /usr/bin/python2

# Host variables
webserver_python_interpreter: /usr/bin/python3

# Tasks
- name: Install Python3
  apt:
    name: python3
  when: ansible_python_interpreter != hostvars['webserver_python_interpreter']
```

In this example, the `ansible_python_interpreter` variable is defined in the global scope. However, the `webserver_python_interpreter` variable is defined in the host scope for the host `example.com`. This means that the `Install Python3` task will only be executed on the host `example.com`.

By understanding how variable scopes work in Ansible, you can write more efficient and reusable playbooks.
