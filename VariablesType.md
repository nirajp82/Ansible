## Different types of variables

### Scalar Variable:

**Example:**
```yaml
app_name: "My Awesome Web App"
```

**Explanation:**
In this example, `app_name` is a scalar variable storing the name of your web application. Scalar variables are used for single values like strings, representing straightforward, single-piece information like the application's name.

### Boolean Variable:

**Example:**
```yaml
enable_https: true
```

**Explanation:**
The `enable_https` variable is boolean, representing whether your web application should use HTTPS (`true`) or not (`false`). Boolean variables are useful for enabling or disabling certain features or behaviors.

### Numeric Variable:

**Example:**
```yaml
max_connections: 1000
```

**Explanation:**
Here, `max_connections` is a numeric variable indicating the maximum number of connections your application can handle. Numeric variables are essential for numerical values such as counts, sizes, or limits.

### List Variable:

**Example:**
```yaml
allowed_ports:
  - 80
  - 443
  - 8080
```

**Explanation:**
`allowed_ports` is a list variable containing a list of ports where your web application can listen for incoming traffic. List variables are useful for storing multiple related values, like a list of ports, usernames, or services.

### Dictionary Variable:

**Example:**
```yaml
database_config:
  host: "db.example.com"
  port: 5432
  username: "dbuser"
  password: "secretpassword"
```

**Explanation:**
In this case, `database_config` is a dictionary variable containing key-value pairs representing database connection details. Dictionary variables are powerful for storing structured data, like configuration settings for different components of your application.

### Null/Empty Variable:

**Example:**
```yaml
temporary_data: null
```

**Explanation:**
`temporary_data` is set to `null`, indicating that there is no specific value assigned. Null variables can be useful when a variable is not yet defined or when indicating the absence of a value.

These examples illustrate how different types of variables can be applied in a real-world scenario, providing flexibility and clarity in your Ansible playbooks and configurations.
