## Different types of variables
## Ansible Variables Overview

In this section, we'll explore various types of Ansible variables and their usage examples.

### Table of Contents

1. **Scalar Variable**
2. **Boolean Variable**
3. **Numeric Variable**
4. **List Variable**
5. **Dictionary Variable**
6. **Null/Empty Variable**

---

## 1. Scalar Variable

**Example:**
```yaml
app_name: "My Awesome Web App"
```

**Usage Example:**
```yaml
- name: Deploy Web Application
  debug:
    msg: "Deploying {{ app_name }}"
```

**Explanation:**
Scalar variables hold single values like strings. In this example, `app_name` represents the name of a web application. It's utilized in a playbook task to display the application's name when deploying.

## 2. Boolean Variable

**Example:**
```yaml
enable_https: true
```

**Usage Example:**
```yaml
- name: Configure HTTPS
  debug:
    msg: "Enabling HTTPS"
  when: enable_https
```

**Explanation:**
Boolean variables represent true or false values. `enable_https` being `true` indicates enabling HTTPS. The `when` statement ensures the debug message appears only when `enable_https` is set to `true`.

## 3. Numeric Variable

**Example:**
```yaml
max_connections: 1000
```

**Usage Example:**
```yaml
- name: Set Maximum Connections
  debug:
    msg: "Maximum Connections: {{ max_connections }}"
```

**Explanation:**
Numeric variables store numerical values. `max_connections` with a value of `1000` indicates the maximum number of connections. It's utilized in a task to display the configured maximum connections.

## 4. List Variable

**Example:**
```yaml
allowed_ports:
  - 80
  - 443
  - 8080
```

**Usage Example:**
```yaml
- name: Open Firewall Ports
  debug:
    msg: "Opening port {{ item }}"
  with_items: "{{ allowed_ports }}"
```

**Explanation:**
In this scenario, `allowed_ports` is a list variable containing multiple port numbers. The `with_items` loop iterates over each port in the list and uses `{{ item }}` to display individual ports within the debug message. For each iteration, a debug message is printed, indicating the port being processed.

**Detailed Explanation:**
- `allowed_ports` is a list variable storing `[80, 443, 8080]`.
- `with_items: "{{ allowed_ports }}"` enables iteration over each item in the list.
- `{{ item }}` represents the current port being processed during each iteration.
- The debug message is printed multiple times, once for each port in the list, displaying the port number being opened in the firewall.

This detailed explanation provides insight into how list variables are defined and utilized in Ansible playbooks, enabling dynamic and iterative operations within tasks.

## 5. Dictionary Variable

**Example:**
```yaml
database_config:
  host: "db.example.com"
  port: 5432
  username: "dbuser"
  password: "secretpassword"
```

**Usage Example:**
```yaml
- name: Configure Database Connection
  debug:
    msg: "Connecting to {{ database_config.host }} on port {{ database_config.port }}"
```

**Explanation:**
Dictionary variables store key-value pairs. `database_config` contains configuration details for a database connection. The keys (`host`, `port`) are used to customize the debug message in the playbook task.

## 6. Null/Empty Variable

**Example:**
```yaml
temporary_data: null
```

**Usage Example:**
```yaml
- name: Handle Temporary Data
  debug:
    msg: "Temporary data is {{ temporary_data|default('not set') }}"
```

**Explanation:**
Null/empty variables represent the absence of a value. `temporary_data` set to `null` is used in a playbook task. The `|default('not set')` filter ensures a default message is displayed if `temporary_data` is not set.

---

This structured format provides clear navigation to detailed explanations for each variable type in Ansible.
