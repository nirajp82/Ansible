Conditional statements in Ansible allow you to execute tasks based on certain conditions. For example, you can use a conditional statement to install a different package on Debian systems than on RHEL systems. Or, you can use a conditional statement to skip a task if a certain file is already present on the remote system.

To use a conditional statement in Ansible, you use the `when` keyword. The `when` keyword takes a boolean expression as its argument. The task will only be executed if the boolean expression evaluates to `true`.

Here is an example of a conditional statement in Ansible:

```yaml
- hosts: all
  tasks:
  - name: Install Apache
    yum:
      name: httpd
      state: present
    when: ansible_distribution == 'CentOS'
```

This task will only be executed if the remote system is running CentOS. If the remote system is running a different distribution, the task will be skipped.

You can also use the `and` and `or` operators in conditional statements. The `and` operator means that both conditions must be met in order for the task to be executed. The `or` operator means that either condition can be met in order for the task to be executed.

Here is an example of a conditional statement that uses the `and` operator:

```yaml
- hosts: all
  tasks:
  - name: Install Apache and MySQL
    yum:
      name:
      - httpd
      - mysql
      state: present
    when: ansible_distribution == 'CentOS' and ansible_memory_mb >= 4096
```

This task will only be executed if the remote system is running CentOS and has at least 4GB of memory.

Here is an example of a conditional statement that uses the `or` operator:

```yaml
- hosts: all
  tasks:
  - name: Install Apache or Nginx
    yum:
      name:
      - httpd
      - nginx
      state: present
    when: ansible_distribution == 'CentOS' or ansible_distribution == 'Debian'
```

This task will be executed if the remote system is running either CentOS or Debian.


Sure, here is an example of a conditional statement in Ansible that is based on host facts, variables, and is reusable:

```yaml
- hosts: all
  vars:
    # Get the fact for the number of CPU cores
    cpu_cores: "{{ ansible_processor_cores }}"
    # Define a variable for the Apache server role
    apache_server_role: "webserver"

    # Check if the host has at least 4 CPU cores
    has_4_cpu_cores: "{{ cpu_cores >= 4 }}"

    # Check if the host is already configured as an Apache server
    is_apache_server_configured: "{{ lookup('file', '/etc/apache2/httpd.conf').exists }}"

  tasks:
    - name: Configure Apache server
      apache2_conf:
        name: "httpd.conf"
        state: present
        content: |
          ServerTokens Prod
          KeepAlive On
          MaxKeepAliveRequests 100

          # Configure the number of worker processes
          WorkerProcesses {{ has_4_cpu_cores | ternary(4, 2) }}
          MaxConnectionsPerChild 100

      when: has_4_cpu_cores and not is_apache_server_configured

    - name: Restart Apache server
      service:
        name: apache2
        state: restarted
      when: has_4_cpu_cores and not is_apache_server_configured
```

This playbook uses the following host facts, variables, and re-usability:

* `ansible_processor_cores`: The number of CPU cores on the host.
* `apache_server_role`: A variable that is set to the role of the Apache server, for example `webserver` or `loadbalancer`.
* `has_4_cpu_cores`: A variable that is set to `True` if the host has at least 4 CPU cores, and `False` otherwise.
* `is_apache_server_configured`: A variable that is set to `True` if the host is already configured as an Apache server, and `False` otherwise.

The conditional statement in the `when` clause of the `Configure Apache server` task checks if the following conditions are met:

* The host has at least 4 CPU cores.
* The host is not already configured as an Apache server.

If both of these conditions are met, the task will be executed. This task will configure the Apache server on the host.

The conditional statement in the `when` clause of the `Restart Apache server` task checks if the following conditions are met:

* The host has at least 4 CPU cores.
* The host is not already configured as an Apache server.

If both of these conditions are met, the task will be executed. This task will restart the Apache server on the host.

This playbook is reusable because it uses variables and facts to determine which tasks should be executed. This means that you can use this playbook to configure Apache servers on any host, regardless of the specific version of Linux that is installed. You can also easily modify this playbook to configure other types of servers, simply by changing the value of the `apache_server_role` variable and the tasks that are executed.

Note, The content parameter is a multi-line string that contains the configuration directives that you want to add to the httpd.conf file.

The following configuration directives are being added to the httpd.conf file:

* ServerTokens Prod: This directive sets the ServerTokens header to Prod, which prevents the Apache server from revealing its version number in the response headers.
* KeepAlive On: This directive enables keep-alive connections, which allows browsers to reuse the same connection to the Apache server for multiple requests. This can improve the performance of your website.
* MaxKeepAliveRequests 100: This directive sets the maximum number of requests that a keep-alive connection can handle to 100. This is a good default value for most websites.
* Once this task has been executed, the Apache server will be configured with the specified configuration directives.

