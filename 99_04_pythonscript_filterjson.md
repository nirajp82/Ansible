Certainly! Let me provide you with a comprehensive example that includes an Ansible playbook, a Python script, and JSON data. In this example, the Ansible playbook will run the Python script to filter records from a JSON file where the country is "USA" and the state is "MD". The filtered data will be saved in a new JSON file. 

### JSON Data (`data.json`):

```json
[
    {
        "firstname": "John",
        "lastname": "Doe",
        "country": "USA",
        "state": "MD"
    },
    {
        "firstname": "Alice",
        "lastname": "Smith",
        "country": "USA",
        "state": "CA"
    },
    {
        "firstname": "Bob",
        "lastname": "Johnson",
        "country": "USA",
        "state": "MD"
    },
    {
        "firstname": "Eva",
        "lastname": "Brown",
        "country": "USA",
        "state": "NY"
    }
]
```

### Python Script (`filter_json.py`):

```python
import json

def filter_records(input_file, output_file):
    filtered_data = []
    with open(input_file, 'r') as file:
        data = json.load(file)
        for record in data:
            if record.get('country') == 'USA' and record.get('state') == 'MD':
                employee_info = {
                    'firstname': record.get('firstname'),
                    'lastname': record.get('lastname')
                }
                filtered_data.append(employee_info)
    with open(output_file, 'w') as output_file:
        json.dump(filtered_data, output_file, indent=4)

if __name__ == "__main__":
    input_file_path = input("Enter the path of the input JSON file: ")
    output_file_path = input("Enter the path for the filtered output JSON file: ")
    filter_records(input_file_path, output_file_path)
    print("Filtered data has been saved to", output_file_path)
```

### Ansible Playbook (`filter_data.yml`):

```yaml
---
- name: Filter JSON Data
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Copy Python Script to Remote Host
      ansible.builtin.copy:
        src: filter_json.py
        dest: /tmp/filter_json.py
        mode: '0755'

    - name: Run Python Script to Filter Data
      ansible.builtin.command: /usr/bin/python3 /tmp/filter_json.py
      register: script_output
      changed_when: false
      ignore_errors: true

    - name: Display Script Output
      debug:
        var: script_output.stdout_lines
      when: script_output.rc == 0
```

Explanation:

1. **JSON Data (`data.json`)**: This file contains the input data with employee records.

2. **Python Script (`filter_json.py`)**: This Python script reads the JSON file, filters records where the country is "USA" and the state is "MD", and saves the filtered data to the specified output file.

3. **Ansible Playbook (`filter_data.yml`)**: This playbook copies the Python script to the remote host, executes it, and displays the script output. `changed_when: false` ensures that Ansible does not consider the task as changed even if the script is executed. `ignore_errors: true` allows the playbook to continue running other tasks even if the script fails.

To run the Ansible playbook, use the following command:

```bash
ansible-playbook filter_data.yml
```

Make sure to provide the correct paths for the input JSON file and specify the path where you want to save the filtered output JSON file when prompted by the Python script.
