**Ansible Inventory Files: An Overview**

**Introduction:**

Ansible automates tasks on managed nodes or “hosts” in your infrastructure, using a list or group of lists known as inventory. You can pass host names at the command line, but most Ansible users create inventory files. Your inventory defines the managed nodes you automate, with groups so you can run automation tasks on multiple hosts at the same time. Once your inventory is defined, you use patterns to select the hosts or groups you want Ansible to run against.

The inventory file is typically named `inventory` and can be placed in the root directory of your Ansible project. However, Ansible allows multiple inventory files, and you can specify their location using the `-i` option when running Ansible commands

# Sample Inventory File
```ini  
# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

# Database Servers
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Password123!

[web_servers]
web1
web2

[db_servers]
db1
```

### Default Groups

Even if you do not define any groups in your inventory file, Ansible creates two default groups: **all** and **ungrouped**. 

- The **all** group contains every host in your inventory.
  
- The **ungrouped** group contains all hosts that don’t belong to any other group except **all**. 

Every host will always belong to at least two groups: **all** and **ungrouped**, or **all** and some other specific group.

For example, in the basic inventory setup mentioned above:

- The host `mail.example.com` belongs to the **all** group and the **ungrouped** group.
  
- The host `two.example.com` belongs to the **all** group and the **dbservers** group.

Though **all** and **ungrouped** are always present, they might not explicitly appear in group listings like `group_names`.

### Hosts in Multiple Groups

It's common to assign each host to more than one group. For instance, a production web server in a datacenter in Atlanta might be included in groups like `[prod]`, `[atlanta]`, and `[webservers]`.

Groups can help track:

- **What:** An application, stack, or microservice (e.g., database servers, web servers).
  
- **Where:** A datacenter or region for local DNS, storage, etc. (e.g., east, west).

- **When:** The development stage to avoid testing on production resources (e.g., prod, test).

This grouping system provides flexibility, allowing hosts to be organized based on different criteria, aiding in efficient configuration management and automation strategies.

```ini
ungrouped:
  hosts:
    mail.example.com:
webservers:
  hosts:
    foo.example.com:
    bar.example.com:
dbservers:
  hosts:
    one.example.com:
    two.example.com:
    three.example.com:
east:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
west:
  hosts:
    bar.example.com:
    three.example.com:
prod:
  hosts:
    foo.example.com:
    one.example.com:
    two.example.com:
test:
  hosts:
    bar.example.com:
    three.example.com:
```

### Grouping Groups: Parent/Child Group Relationships

You can establish parent/child relationships among groups in Ansible, where parent groups contain nested groups or groups of groups. This approach allows for a more organized and modular structure in your inventory.

#### Creating Parent/Child Relationships:

- **INI Format:** Use the `:children` suffix.
- **YAML Format:** Use the `children:` entry.

Here's an example of the same inventory as shown before, simplified with parent groups for `prod` and `test`:

```yaml
ungrouped:
  hosts:
    - mail.example.com

webservers:
  hosts:
    - foo.example.com
    - bar.example.com

dbservers:
  hosts:
    - one.example.com
    - two.example.com
    - three.example.com

east:
  hosts:
    - foo.example.com
    - one.example.com
    - two.example.com

west:
  hosts:
    - bar.example.com
    - three.example.com

prod:
  children:
    - east
    - webservers

test:
  children:
    - west
```

**Properties of Child Groups:**

- Any host that is a member of a child group is automatically considered a member of the parent group.
- Groups can have multiple parents and children, but circular relationships are not allowed.
- Hosts can be in multiple groups, but at runtime, there will be only one instance of a host. Ansible merges data from multiple groups for the same host.

This hierarchical organization simplifies management and configuration by allowing you to handle larger infrastructure in smaller, more manageable chunks.

### **Parameters:**

Certainly! Below is a list of commonly used Ansible parameters in inventory files along with their descriptions:

1. **ansible_host:**
   - Specifies the IP address or hostname of the server.
   - Example: `ansible_host=192.168.1.101`
  
2. **ansible_connection:**
   - Specifies the connection type, such as `ssh`, `winrm`, `paramiko`, or `local`.
   - Example: `ansible_connection=ssh`
     - `SSH`: Linux based system
     - `winrm`: Windows based system

2. **ansible_port:**
   - Specifies the SSH port number of the server. Default is 22.
   - Example: `ansible_port=2222`

3. **ansible_user:**
   - Sets the SSH username for connecting to the server.
   - Example: `ansible_user=admin`

4. **ansible_ssh_private_key_file:**
   - Defines the path to the private SSH key file for authentication.
   - Example: `ansible_ssh_private_key_file=/path/to/private_key`

5. **ansible_ssh_pass:**
   - Sets the SSH password for connecting to the server.
   - Example: `ansible_ssh_pass=my_password`

6. **ansible_password:**
   - Set password for Windows based hosts

6. **ansible_become:**
   - Enables or disables privilege escalation (sudo) on the server.
   - Example: `ansible_become=yes`

7. **ansible_become_user:**
   - Specifies the user to switch to when using privilege escalation.
   - Example: `ansible_become_user=root`

8. **ansible_become_method:**
   - Specifies the method used for privilege escalation, such as `sudo` or `su`.
   - Example: `ansible_become_method=sudo`

10. **ansible_python_interpreter:**
    - Sets the path to the Python interpreter on the remote server.
    - Example: `ansible_python_interpreter=/usr/bin/python3`

11. **ansible_shell_type:**
    - Specifies the shell type used for running commands on the server, such as `sh` or `csh`.
    - Example: `ansible_shell_type=bash`

12. **ansible_shell_executable:**
    - Specifies the path to the shell executable on the remote server.
    - Example: `ansible_shell_executable=/bin/bash`

13. **ansible_winrm_scheme:**
    - Specifies the communication protocol used for WinRM connections (used in Windows environments).
    - Example: `ansible_winrm_scheme=http`

14. **ansible_winrm_transport:**
    - Specifies the transport method for WinRM connections, such as `basic` or `kerberos`.
    - Example: `ansible_winrm_transport=basic`

15. **ansible_winrm_server_cert_validation:**
    - Enables or disables server certificate validation for WinRM connections.
    - Example: `ansible_winrm_server_cert_validation=False`

These parameters provide flexibility and control when connecting to and managing remote servers using Ansible. They allow you to customize the connection and authentication methods according to your specific infrastructure requirements.


### Inventory Aliases

You can define aliases in your Ansible inventory using host variables:

#### INI Format:

```ini
[jumper]
ansible_port=5555
ansible_host=192.0.2.50
```

In the INI format, the `[jumper]` section defines the host alias, and the `ansible_port` and `ansible_host` variables specify the SSH port and IP address, respectively.

#### YAML Format:

```yaml
---
jumper:
  ansible_port: 5555
  ansible_host: 192.0.2.50
```

In the YAML format, the host alias "jumper" is defined as a key, and the associated connection parameters (`ansible_port` and `ansible_host`) are specified as key-value pairs.

When you run Ansible commands against the host alias "jumper" Ansible will use the defined connection parameters (`ansible_port=5555` and `ansible_host=192.0.2.50`) to establish the SSH connection to the remote host at the specified IP address and port number. This method enhances readability and simplifies the management of connection details for specific hosts or aliases in your Ansible inventory files.

Example of how you might run a simple Ansible ad-hoc command using the "jumper" alias:

```bash
ansible jumper -m ping
```

In this command:

- `ansible` is the Ansible command-line tool.
- `jumper` is the alias specified in your inventory file.
- `-m ping` specifies the Ansible module to run, in this case, the "ping" module which tests connectivity to the target hosts.

When you run this command, Ansible will use the defined connection parameters (port `5555` and host `192.0.2.50`) associated with the "jumper" alias to establish an SSH connection and execute the ping module on the remote host.

This is just a basic example; you can replace `-m ping` with any other module and provide additional options as needed for your specific use case.
