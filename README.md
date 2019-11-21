Please note that every role is inheriting handlers from global/handlers/main.yml. Ansible will also look at every role's handler/main.yml file as well for that role which can be used to add role specific handlers as well as override global handlers. 

What this repo contains:
```
Main.yml: Uses variables from roles/global/vars/main.yml and does the following:
    1) Set up an ubuntu server from scratch.
    2) Disable sudo login
    3) Set up Mail user and send emails to root that can be forwarded to your inbox
    4) Automate SSL renewal of your domain
    5) Sets up nginx Webserver as well as gunicorn
    6) Sets up Postgresql DB service and connects to the django project
    7) Sets up Memcached server side caching framework and listens on port 11211.
    8) Sets up rules to ban IPs that are trying to "sniff".
```
```
deploy.yml: Uses variables from roles/global/vars/main.yml and does the following:
    1) Deploys a new Django project from the specified Github Repo
```

Note: These scripts are assuming that static files are being served by an external service like Digital ocean spaces or AWS S3. Nginx Config as a result has no configuration for serving static files.
The file main.yml imports every role and can be used to setup an ubuntu server from scratch.


Please note that for extra security the deploy key is passed to ansible via SSH-agent. This gets around putting the private key on the server.
The public key should be attached to your code repo.


The DB Password (db_user_password) has been ecnrypted using `encrypt_string` command

Think of the password you would like and run the command: `ansible-vault encrypt_string [DB_USER_PASSWORD] `. This password must be set as (db_user_password) in global/vars 
It will then ask you to choose a vault password. *Please remember the vault password because that needs to be entered when either main.yml or deploy.yml is run*


This password would be asked by you when you run the playbook and would be saved in server RAM and later used to decrypt the password.
 This can be the same or different from the password you wanted to encrypt in the first place.
 
 
 Now append `--ask-vault-pass` flag to every playbook you run. 
  
 `ansible-playbook main.yml --ask-vault-pass -i hosts` or `ansible-playbook deploy.yml --ask-vault-pass -i hosts`
 
 ### Notes:
 ```
 1) Ansible doesn't automatically disable bash history when password is entered. These are the special characters that invoke bash history. https://www.tldp.org/LDP/abs/html/histcommands.html
    1) A way around this is to run `set +o history` before you run `ansible-vault encrypt_string [DB_USER_PASSWORD] ` and then run `set -o history` to enable bash history. The first command disables command line bash history and the last command enables it.
 2) Please note that in case you are using piped outputs in bash. Then commands before and after  '|' will not inherit SUDO in case any command is run with that.
 3) All variables can be set in roles/global/vars/main.yml
```

# Aside:
To flush all content from memcache use ```echo "flush_all" | nc localhost 11211```