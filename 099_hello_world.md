To create an Ansible playbook that prints "Hello, World!", you can follow these steps:

1. Create a new file named `hello_world.yml` for your Ansible playbook.

2. Add the following content to the `hello_world.yml` file:

```yaml
---
- name: Print Hello, World!
  hosts: localhost  # This playbook will run on the localhost
  tasks:
    - name: Print Hello, World!
      debug:
        msg: "Hello, World!"
```

In this playbook:

- `name` specifies the description of the playbook.
- `hosts` specifies the target hosts where the playbook will run. In this case, it's set to `localhost`.
- `tasks` contains a list of tasks to be executed. The only task in this playbook prints "Hello, World!" using the `debug` module.

3. Save the `hello_world.yml` file.

4. To run the Ansible playbook, you can use the following command in your terminal:

```bash
ansible-playbook hello_world.yml
```

This command will execute the playbook, and you should see the output:

```
PLAY [Print Hello, World!] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [Print Hello, World!] ******************************************************
ok: [localhost] => {
    "msg": "Hello, World!"
}

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

As a result, it will print "Hello, World!" to the console.
