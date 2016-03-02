# open-corroborator-deploy

Deployment routines for Corroborator.

## Installation

Install Ansible:

```
sudo apt-get install ansible
```

To test deployment on local virtual machines, install Vagrant from https://www.vagrantup.com/downloads.html


## VM install

To create a local virtual machine (VM) and install the software stack onto it:

```
vagrant up
```

This uses Vagrant to create the VM and then Vagrant uses Ansible to install the stack with an environment of 'vm' which uses the ansible `group_vars/vm` settings and the Django `corroborator/settings/vm.py` configuration.

To test the VM, visit http://10.33.33.4/ and login as 'admin' with a password of 'password'.

To re-deploy to the running VM:

```
vagrant provision
```

or

```
ansible-playbook -i ansible/vm ansible/site.yml
```

To stop the local VM:

```
vagrant halt
```

To destroy the local VM:

```
vagrant destroy server1
```

## Other install
To use Ansible to install the stack to another target/environment, e.g. _ENV_:

### Create deployment files
In this corroborator-deploy project:

 * create an Ansible inventory file, ENV, to give the target machine location - **make sure** the prefix to the `:children` entry is updated to ENV:
 
```
[ENV:children]
webservers
```

 * create an Ansible groups_vars/ENV directory and main.yml file to give the server name, git branch, database password, etc.
 
In the open-corroborator project:

 * create a Django settings file, corroborator/settings/ENV.py, to give the media locations, initial (non-secret) database password, etc.
 
By default, uploaded media files are stored and served from the local disk. To store them in Amazon S3, set `QUEUED_STORAGE=True` in the Django settings file and set the `AWS_ACCESS_KEY_ID` and `AWS_STORAGE_BUCKET_NAME`).
If PROD_BUILD is set to True (to use a prebuilt javascript file), you should also set the Ansible variable `run_grunt: yes` (to make sure the file is rebuilt on the server)

### Deploy
```
cd ansible
ansible-playbook -i ENV site.yml -K
```

Then make secure by setting the secret settings: login to the target machine and:
 
   a. manually change the secret database password settings etc. in `/var/local/sites/corroborator/lib/python2.7/site-packages/corroborator/settings/ENV.py`
      (including `AWS_SECRET_ACCESS_KEY` if S3 queued storage is being used)
      
   b. change the mysql database password to suit
   
   c. restart the services:
   
   ```
   sudo service corroborator-gunicorn restart
   sudo service corroborator-celery restart
   ```

To test, visit the ENV server in a browser and login as 'admin' with a password of 'password'.
