# Add a service
1. Define a new role with at least the following tasks:
   - `main.yml` to handle the installation of the service
   - `stop.yml` to handle the deactivation of the service
2. In the `hosts` file, add a new entry in the `install` dictionary.

## Nginx configuration
If the service should be accessible through nginx, add the following into the `main.yml` task:
```
- name: Copy nginx configuration
  ansible.builtin.import_tasks:
    file: utilities/add_nginx_conf.yml
  vars:
    conf_src_path: "nginx.conf.j2"
    label: "{{ label-to-identify-the-service }}"
```
Assuming that a `nginx.conf.j2` is defined, it will copy it into the nginx include directory.


## Gitlab SSO configuration
If the service can be configured with Gitlab SSO, create a new task to handle the setup.
Import the task in the `sso.yml` playbook.


## Dashboard link configuration
To configure a link visible in the homepage, add a new entry in the `services.yml` task of the `dashboard` role (`roles/dashboard/tasks/services.yml`).


# Remove a service

## Soft remove
To just stop a service, modify the `install` dictionary of the `hosts` file and re-run the `main.yml` playbook.

## Complete remove
To completely remove a service, the following steps are needed:
1. Delete its role
2. Delete its entry in the `install` dictionary of the `hosts` file
3. If needed, delete its entry from the `dashboard` role `services.yml` task
3. If needed, delete its entry from the `sso.yml` playbook