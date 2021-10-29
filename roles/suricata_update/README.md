# suricata_deploy
## **Deploy Suricata**

**Role Description**

This role will update suricata, optionally configure suricata, and push the updated rules out to IDS servers.  It has been setup modularly so different tasks can be shut on and off for development and troubleshooting.

**Dependencies**

Suricata installed on the Admin server.  Suricata installed on the clients

**Required Variables**

This role determines which tasks to run based on the ansible_distribution_major_version and ansible_os_family variables.  For this reason gather_facts is set to true on the calling playbook.

```yaml
---
suricata_config: false                        #Run the Suricata config tasks file
suricata_update: true                         #Run the Suricata update tasks file
suricata_push: true                           #Run the Suricata push tasks file
suricata_validate: true                       #Run the Suricata validate tasks file
suricata_admin_server: servername.example.com #Admin server that runs the update
suricata_dir: /var/lib/suricata/rules         #Directory where rules are stored

```

**Example Playbook Usage**

```yaml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: import suricata_update role
      import_role:
        name: suricata_update
```

**Ansible Tower Template Settings**

Limit should have "Prompt on Launch" set for the template.  Include the host name to install to when prompted.

If not specified in defaults/main.yml you would include the required variables in the extra_variables section of the template.  Also if toggling certain tasks files are desired they could be specified in extra_vars as well.

**EXTRA VARIABLES**

```yaml
---
suricata_update: false

```

**FILES**

If suricata_config tasks is desired, place the systemd suricata.service file and the /etc/suricata/update.yaml file in the files directory

**HANDLERS**

There are two handlers, "Reload Suricata Rules" and "Restart Suricata".  Currently the code calls "Restart Suricata" handler as the intent is to fail the service if there are syntax errors in the rules file.

**TASKS**

```yaml
main.yml               #The main tasks that includes the other tasks on conditionals
suricata_config.yml    #Copy over the config files to admin_server (update.yaml) and to
                       #the clients (systemd service file to include the
                       # --init-errors-fatal option)
suricata_push.yml      #Fetch rules from Admin server and push to the clients
suricata_update.yml    #Run update script on Admin server
suricata_validate.yml  #Validate that the Suricata service started back up and fail the
                       #job if it is not running
              
```
