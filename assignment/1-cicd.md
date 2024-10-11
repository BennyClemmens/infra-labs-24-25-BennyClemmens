# Lab 1: Continuous Integration/Delivery with Jenkins

In this lab assignment, you will learn the basics on how to set up a build pipeline with Jenkins. In this lab assignment, we will leverage Docker as a platform to easily install and run a Jenkins server. Remark that in a real-world setting, you would probably have a dedicated build server.

## Learning Goals

- Installing and running Jenkins in a Docker container
- Creating simple jobs and build pipelines
- Running the pipeline to build and test an application, and to deploy changes in the application

## Acceptance criteria

- Show that you created a GitHub repository for the sample application
- Show that the application is running by opening it in a web browser
- Show the overview of jobs in the Jenkins dashboard
- Make a change to the sample application, commit and push
- Launch the build pipeline and show the change to the application in the browser
- Show your lab report and cheat sheet! It should contain screenshots of consecutive steps and console output of commands you used.

## 1.1 Set up the lab environment

For this lab assignment, we'll be using the `dockerlab` environment. Start the `dockerlab` VM and log in with `vagrant ssh`. This VM is an Ubuntu 20.04 system with Docker installed. A container will already be running (check this!) with Portainer, a webinterface to manage containers. It can be accessed on <http://192.168.56.20:9000/>. You will be asked to create a password for the admin user. Portainer is not part of this assignment, but you can use it to inspect the running containers.

```bash
Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant status
==> vagrant: A new version of Vagrant is available: 2.4.1 (installed version: 2.3.7)!
==> vagrant: To upgrade visit: https://www.vagrantup.com/downloads.html

Current machine states:

dockerlab                 not created (virtualbox)

The environment has not yet been created. Run `vagrant up` to
create the environment. If a machine is not created, only the
default provider will be shown. So if a provider is not listed,
then the machine is not created for that environment.

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant up
Bringing machine 'dockerlab' up with 'virtualbox' provider...
==> dockerlab: Box 'bento/ubuntu-24.04' could not be found. Attempting to find and install...
    dockerlab: Box Provider: virtualbox
    dockerlab: Box Version: >= 0
==> dockerlab: Loading metadata for box 'bento/ubuntu-24.04'
    dockerlab: URL: https://vagrantcloud.com/bento/ubuntu-24.04
==> dockerlab: Adding box 'bento/ubuntu-24.04' (v202404.26.0) for provider: virtualbox
    dockerlab: Downloading: https://vagrantcloud.com/bento/boxes/ubuntu-24.04/versions/202404.26.0/providers/virtualbox/amd64/vagrant.box
    dockerlab:
==> dockerlab: Successfully added box 'bento/ubuntu-24.04' (v202404.26.0) for 'virtualbox'!
==> dockerlab: Importing base box 'bento/ubuntu-24.04'...
==> dockerlab: Matching MAC address for NAT networking...
==> dockerlab: Checking if box 'bento/ubuntu-24.04' version '202404.26.0' is up to date...
==> dockerlab: Setting the name of the VM: dockerlab_dockerlab_1728631102070_51583
==> dockerlab: Clearing any previously set network interfaces...
==> dockerlab: Preparing network interfaces based on configuration...
    dockerlab: Adapter 1: nat
    dockerlab: Adapter 2: hostonly
==> dockerlab: Forwarding ports...
    dockerlab: 22 (guest) => 2222 (host) (adapter 1)
==> dockerlab: Running 'pre-boot' VM customizations...
==> dockerlab: Booting VM...
==> dockerlab: Waiting for machine to boot. This may take a few minutes...
    dockerlab: SSH address: 127.0.0.1:2222
    dockerlab: SSH username: vagrant
    dockerlab: SSH auth method: private key
    dockerlab:
    dockerlab: Vagrant insecure key detected. Vagrant will automatically replace
    dockerlab: this with a newly generated keypair for better security.
    dockerlab:
    dockerlab: Inserting generated public key within guest...
    dockerlab: Removing insecure key from the guest if it's present...
    dockerlab: Key inserted! Disconnecting and reconnecting using new SSH key...
==> dockerlab: Machine booted and ready!
==> dockerlab: Checking for guest additions in VM...
==> dockerlab: Setting hostname...
==> dockerlab: Configuring and enabling network interfaces...
==> dockerlab: Mounting shared folders...
    dockerlab: /vagrant => C:/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab
==> dockerlab: Running provisioner: ansible_local...
    dockerlab: Installing Ansible...
The requested Ansible compatibility mode (2.0) is in conflict with
the Ansible installation on your Vagrant guest system (currently: 10.5.0).
See https://docs.vagrantup.com/v2/provisioning/ansible_common.html#compatibility_mode
for more information.
```

`some vagrant/ansible problem that needs to be resolved first ...`

- VagrantFile
  - `ansible.compatibility_mode = '2.0'` => `ansible.compatibility_mode = 'auto'`

```bash
Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant destroy -f
==> dockerlab: Forcing shutdown of VM...
==> dockerlab: Destroying VM and associated drives...

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant up
Bringing machine 'dockerlab' up with 'virtualbox' provider...
==> dockerlab: Importing base box 'bento/ubuntu-24.04'...
==> dockerlab: Matching MAC address for NAT networking...
==> dockerlab: Checking if box 'bento/ubuntu-24.04' version '202404.26.0' is up to date...
==> dockerlab: Setting the name of the VM: dockerlab_dockerlab_1728641516159_26255
==> dockerlab: Clearing any previously set network interfaces...
==> dockerlab: Preparing network interfaces based on configuration...
    dockerlab: Adapter 1: nat
    dockerlab: Adapter 2: hostonly
==> dockerlab: Forwarding ports...
    dockerlab: 22 (guest) => 2222 (host) (adapter 1)
==> dockerlab: Running 'pre-boot' VM customizations...
==> dockerlab: Booting VM...
==> dockerlab: Waiting for machine to boot. This may take a few minutes...
    dockerlab: SSH address: 127.0.0.1:2222
    dockerlab: SSH username: vagrant
    dockerlab: SSH auth method: private key
    dockerlab:
    dockerlab: Vagrant insecure key detected. Vagrant will automatically replace
    dockerlab: this with a newly generated keypair for better security.
    dockerlab:
    dockerlab: Inserting generated public key within guest...
    dockerlab: Removing insecure key from the guest if it's present...
    dockerlab: Key inserted! Disconnecting and reconnecting using new SSH key...
==> dockerlab: Machine booted and ready!
==> dockerlab: Checking for guest additions in VM...
==> dockerlab: Setting hostname...
==> dockerlab: Configuring and enabling network interfaces...
==> dockerlab: Mounting shared folders...
    dockerlab: /vagrant => C:/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab
==> dockerlab: Running provisioner: ansible_local...
    dockerlab: Installing Ansible...
    dockerlab: Running ansible-playbook...
usage: ansible-playbook [-h] [--version] [-v] [--private-key PRIVATE_KEY_FILE]
                        [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
                        [--ssh-common-args SSH_COMMON_ARGS]
                        [--sftp-extra-args SFTP_EXTRA_ARGS]
                        [--scp-extra-args SCP_EXTRA_ARGS]
                        [--ssh-extra-args SSH_EXTRA_ARGS]
                        [-k | --connection-password-file CONNECTION_PASSWORD_FILE]
                        [--force-handlers] [--flush-cache] [-b]
                        [--become-method BECOME_METHOD]
                        [--become-user BECOME_USER]
                        [-K | --become-password-file BECOME_PASSWORD_FILE]
                        [-t TAGS] [--skip-tags SKIP_TAGS] [-C] [-D]
                        [-i INVENTORY] [--list-hosts] [-l SUBSET]
                        [-e EXTRA_VARS] [--vault-id VAULT_IDS]
                        [-J | --vault-password-file VAULT_PASSWORD_FILES]
                        [-f FORKS] [-M MODULE_PATH] [--syntax-check]
                        [--list-tasks] [--list-tags] [--step]
                        [--start-at-task START_AT_TASK]
                        playbook [playbook ...]
ansible-playbook: error: unrecognized arguments: --sudo

usage: ansible-playbook [-h] [--version] [-v] [--private-key PRIVATE_KEY_FILE]
                        [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
                        [--ssh-common-args SSH_COMMON_ARGS]
                        [--sftp-extra-args SFTP_EXTRA_ARGS]
                        [--scp-extra-args SCP_EXTRA_ARGS]
                        [--ssh-extra-args SSH_EXTRA_ARGS]
                        [-k | --connection-password-file CONNECTION_PASSWORD_FILE]
                        [--force-handlers] [--flush-cache] [-b]
                        [--become-method BECOME_METHOD]
                        [--become-user BECOME_USER]
                        [-K | --become-password-file BECOME_PASSWORD_FILE]
                        [-t TAGS] [--skip-tags SKIP_TAGS] [-C] [-D]
                        [-i INVENTORY] [--list-hosts] [-l SUBSET]
                        [-e EXTRA_VARS] [--vault-id VAULT_IDS]
                        [-J | --vault-password-file VAULT_PASSWORD_FILES]
                        [-f FORKS] [-M MODULE_PATH] [--syntax-check]
                        [--list-tasks] [--list-tags] [--step]
                        [--start-at-task START_AT_TASK]
                        playbook [playbook ...]

Runs Ansible playbooks, executing the defined tasks on the targeted hosts.

positional arguments:
  playbook              Playbook(s)

options:
  --become-password-file BECOME_PASSWORD_FILE, --become-pass-file BECOME_PASSWORD_FILE
                        Become password file
  --connection-password-file CONNECTION_PASSWORD_FILE, --conn-pass-file CONNECTION_PASSWORD_FILE
                        Connection password file
  --flush-cache         clear the fact cache for every host in inventory
  --force-handlers      run handlers even if a task fails
  --list-hosts          outputs a list of matching hosts; does not execute
                        anything else
  --list-tags           list all available tags
  --list-tasks          list all tasks that would be executed
  --skip-tags SKIP_TAGS
                        only run plays and tasks whose tags do not match these
                        values. This argument may be specified multiple times.
  --start-at-task START_AT_TASK
                        start the playbook at the task matching this name
  --step                one-step-at-a-time: confirm each task before running
  --syntax-check        perform a syntax check on the playbook, but do not
                        execute it
  --vault-id VAULT_IDS  the vault identity to use. This argument may be
                        specified multiple times.
  --vault-password-file VAULT_PASSWORD_FILES, --vault-pass-file VAULT_PASSWORD_FILES
                        vault password file
  --version             show program's version number, config file location,
                        configured module search path, module location,
                        executable location and exit
  -C, --check           don't make any changes; instead, try to predict some
                        of the changes that may occur
  -D, --diff            when changing (small) files and templates, show the
                        differences in those files; works great with --check
  -J, --ask-vault-password, --ask-vault-pass
                        ask for vault password
  -K, --ask-become-pass
                        ask for privilege escalation password
  -M MODULE_PATH, --module-path MODULE_PATH
                        prepend colon-separated path(s) to module library
                        (default={{ ANSIBLE_HOME ~
                        "/plugins/modules:/usr/share/ansible/plugins/modules"
                        }}). This argument may be specified multiple times.
  -e EXTRA_VARS, --extra-vars EXTRA_VARS
                        set additional variables as key=value or YAML/JSON, if
                        filename prepend with @. This argument may be
                        specified multiple times.
  -f FORKS, --forks FORKS
                        specify number of parallel processes to use
                        (default=5)
  -h, --help            show this help message and exit
  -i INVENTORY, --inventory INVENTORY, --inventory-file INVENTORY
                        specify inventory host path or comma separated host
                        list. --inventory-file is deprecated. This argument
                        may be specified multiple times.
  -k, --ask-pass        ask for connection password
  -l SUBSET, --limit SUBSET
                        further limit selected hosts to an additional pattern
  -t TAGS, --tags TAGS  only run plays and tasks tagged with these values.
                        This argument may be specified multiple times.
  -v, --verbose         Causes Ansible to print more debug messages. Adding
                        multiple -v will increase the verbosity, the builtin
                        plugins currently evaluate up to -vvvvvv. A reasonable
                        level to start is -vvv, connection debugging might
                        require -vvvv. This argument may be specified multiple
                        times.

Connection Options:
  control as whom and how to connect to hosts

  --private-key PRIVATE_KEY_FILE, --key-file PRIVATE_KEY_FILE
                        use this file to authenticate the connection
  --scp-extra-args SCP_EXTRA_ARGS
                        specify extra arguments to pass to scp only (e.g. -l)
  --sftp-extra-args SFTP_EXTRA_ARGS
                        specify extra arguments to pass to sftp only (e.g. -f,
                        -l)
  --ssh-common-args SSH_COMMON_ARGS
                        specify common arguments to pass to sftp/scp/ssh (e.g.
                        ProxyCommand)
  --ssh-extra-args SSH_EXTRA_ARGS
                        specify extra arguments to pass to ssh only (e.g. -R)
  -T TIMEOUT, --timeout TIMEOUT
                        override the connection timeout in seconds (default
                        depends on connection)
  -c CONNECTION, --connection CONNECTION
                        connection type to use (default=ssh)
  -u REMOTE_USER, --user REMOTE_USER
                        connect as this user (default=None)

Privilege Escalation Options:
  control how and which user you become as on target hosts

  --become-method BECOME_METHOD
                        privilege escalation method to use (default=sudo), use
                        `ansible-doc -t become -l` to list valid choices.
  --become-user BECOME_USER
                        run operations as this user (default=root)
  -b, --become          run operations with become (does not imply password
                        prompting)
Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.
```

`vagrant translates ansible.become = true into running ansible with a non existing --sudo`

- VagrantFile
  - `ansible.become = true` => `# ansible.become = true`

```bash
Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant destroy -f
==> dockerlab: Forcing shutdown of VM...
==> dockerlab: Destroying VM and associated drives...

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant up
Bringing machine 'dockerlab' up with 'virtualbox' provider...
==> dockerlab: Importing base box 'bento/ubuntu-24.04'...
==> dockerlab: Matching MAC address for NAT networking...
==> dockerlab: Checking if box 'bento/ubuntu-24.04' version '202404.26.0' is up to date...
==> dockerlab: Setting the name of the VM: dockerlab_dockerlab_1728641808877_4463
==> dockerlab: Clearing any previously set network interfaces...
==> dockerlab: Preparing network interfaces based on configuration...
    dockerlab: Adapter 1: nat
    dockerlab: Adapter 2: hostonly
==> dockerlab: Forwarding ports...
    dockerlab: 22 (guest) => 2222 (host) (adapter 1)
==> dockerlab: Running 'pre-boot' VM customizations...
==> dockerlab: Booting VM...
==> dockerlab: Waiting for machine to boot. This may take a few minutes...
    dockerlab: SSH address: 127.0.0.1:2222
    dockerlab: SSH username: vagrant
    dockerlab: SSH auth method: private key
    dockerlab:
    dockerlab: Vagrant insecure key detected. Vagrant will automatically replace
    dockerlab: this with a newly generated keypair for better security.
    dockerlab:
    dockerlab: Inserting generated public key within guest...
    dockerlab: Removing insecure key from the guest if it's present...
    dockerlab: Key inserted! Disconnecting and reconnecting using new SSH key...
==> dockerlab: Machine booted and ready!
==> dockerlab: Checking for guest additions in VM...
==> dockerlab: Setting hostname...
==> dockerlab: Configuring and enabling network interfaces...
==> dockerlab: Mounting shared folders...
    dockerlab: /vagrant => C:/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab
==> dockerlab: Running provisioner: ansible_local...
    dockerlab: Installing Ansible...
    dockerlab: Running ansible-playbook...

PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [dockerlab]
[WARNING]: Platform linux on host dockerlab is using the discovered Python
interpreter at /usr/bin/python3.12, but future installation of another Python
interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-
core/2.17/reference_appendices/interpreter_discovery.html for more information.

TASK [dockerlab : Include distribution specific variables] *********************
ok: [dockerlab] => (item=/vagrant/ansible/roles/dockerlab/vars/Ubuntu.yml)

TASK [dockerlab : Ensure the necessary (apt) packages are installed] ***********
fatal: [dockerlab]: FAILED! => {"changed": false, "msg": "Failed to lock apt for exclusive operation: Failed to lock directory /var/lib/apt/lists/: E:Could not open lock file /var/lib/apt/lists/lock - open (13: Permission denied)"}

PLAY RECAP *********************************************************************
dockerlab                  : ok=2    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
```

`now the playbook doesn't seem to have the correct rights`

- ansible/site.yml
  - added `become: true`

```bash
Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant destroy -f
==> dockerlab: Forcing shutdown of VM...
==> dockerlab: Destroying VM and associated drives...

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant up
Bringing machine 'dockerlab' up with 'virtualbox' provider...
==> dockerlab: Importing base box 'bento/ubuntu-24.04'...
==> dockerlab: Matching MAC address for NAT networking...
==> dockerlab: Checking if box 'bento/ubuntu-24.04' version '202404.26.0' is up to date...
==> dockerlab: Setting the name of the VM: dockerlab_dockerlab_1728642097284_38928
==> dockerlab: Clearing any previously set network interfaces...
==> dockerlab: Preparing network interfaces based on configuration...
    dockerlab: Adapter 1: nat
    dockerlab: Adapter 2: hostonly
==> dockerlab: Forwarding ports...
    dockerlab: 22 (guest) => 2222 (host) (adapter 1)
==> dockerlab: Running 'pre-boot' VM customizations...
==> dockerlab: Booting VM...
==> dockerlab: Waiting for machine to boot. This may take a few minutes...
    dockerlab: SSH address: 127.0.0.1:2222
    dockerlab: SSH username: vagrant
    dockerlab: SSH auth method: private key
    dockerlab:
    dockerlab: Vagrant insecure key detected. Vagrant will automatically replace
    dockerlab: this with a newly generated keypair for better security.
    dockerlab:
    dockerlab: Inserting generated public key within guest...
    dockerlab: Removing insecure key from the guest if it's present...
    dockerlab: Key inserted! Disconnecting and reconnecting using new SSH key...
==> dockerlab: Machine booted and ready!
==> dockerlab: Checking for guest additions in VM...
==> dockerlab: Setting hostname...
==> dockerlab: Configuring and enabling network interfaces...
==> dockerlab: Mounting shared folders...
    dockerlab: /vagrant => C:/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab
==> dockerlab: Running provisioner: ansible_local...
    dockerlab: Installing Ansible...
    dockerlab: Running ansible-playbook...

PLAY [all] *********************************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Platform linux on host dockerlab is using the discovered Python
interpreter at /usr/bin/python3.12, but future installation of another Python
interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-
core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [dockerlab]

TASK [dockerlab : Include distribution specific variables] *********************
ok: [dockerlab] => (item=/vagrant/ansible/roles/dockerlab/vars/Ubuntu.yml)

TASK [dockerlab : Ensure the necessary (apt) packages are installed] ***********
changed: [dockerlab]

TASK [dockerlab : Ensure the Docker group exists] ******************************
ok: [dockerlab]

TASK [dockerlab : Ensure user Vagrant is a member of the Docker group] *********
changed: [dockerlab]

TASK [dockerlab : Ensure Docker daemon metrics can be scraped by Prometheus] ***
changed: [dockerlab]

TASK [dockerlab : Ensure necessary services are running and enabled] ***********
ok: [dockerlab] => (item=docker.service)

TASK [dockerlab : Enable some useful aliases for managing Docker] **************
changed: [dockerlab]

TASK [dockerlab : Create a Docker volume for persistent data] ******************
changed: [dockerlab] => (item=portainer_data)

TASK [dockerlab : Create a Docker internal network for permanent containers] ***
changed: [dockerlab]

TASK [dockerlab : Create a Docker container for Portainer] *********************
changed: [dockerlab]

RUNNING HANDLER [dockerlab : restart docker] ***********************************
changed: [dockerlab]

PLAY RECAP *********************************************************************
dockerlab                  : ok=12   changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
$ vagrant ssh
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.8.0-31-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Oct 11 10:35:43 AM UTC 2024

  System load:  0.0                Processes:             146
  Usage of /:   16.2% of 30.34GB   Users logged in:       0
  Memory usage: 11%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Use of this system is acceptance of the OS vendor EULA and License Agreements.
vagrant@dockerlab:~$ docker ps
CONTAINER ID   IMAGE                    COMMAND        CREATED          STATUS          PORTS                                                      NAMES
e5664352c46e   portainer/portainer-ce   "/portainer"   10 minutes ago   Up 10 minutes   0.0.0.0:8000->8000/tcp, 0.0.0.0:9000->9000/tcp, 9443/tcp   portainer
vagrant@dockerlab:~$ sudo ss -tlnp
State           Recv-Q          Send-Q                   Local Address:Port                    Peer Address:Port         Process
LISTEN          0               4096                           0.0.0.0:9000                         0.0.0.0:*             users:(("docker-proxy",pid=7366,fd=4))
LISTEN          0               4096                        172.30.0.1:9323                         0.0.0.0:*             users:(("dockerd",pid=7218,fd=21))
LISTEN          0               4096                     127.0.0.53%lo:53                           0.0.0.0:*             users:(("systemd-resolve",pid=6552,fd=15))
LISTEN          0               4096                           0.0.0.0:8000                         0.0.0.0:*             users:(("docker-proxy",pid=7378,fd=4))
LISTEN          0               4096                        127.0.0.54:53                           0.0.0.0:*             users:(("systemd-resolve",pid=6552,fd=17))
LISTEN          0               4096                         127.0.0.1:38175                        0.0.0.0:*             users:(("containerd",pid=5741,fd=10))
LISTEN          0               4096                                 *:22                                 *:*             users:(("sshd",pid=6563,fd=3),("systemd",pid=1,fd=96))
```

![001_portainer](img/001_portainer.PNG)

According to <https://portal.portainer.io/knowledge/your-portainer-instance-has-timed-out-for-security-purposes> this is normal behaviour since I was drinking a cup of coffee.

```bash
vagrant@dockerlab:~$ docker stop portainer
portainer
vagrant@dockerlab:~$ docker start portainer
portainer
vagrant@dockerlab:~$ docker ps
CONTAINER ID   IMAGE                    COMMAND        CREATED          STATUS         PORTS                                                      NAMES
e5664352c46e   portainer/portainer-ce   "/portainer"   18 minutes ago   Up 6 seconds   0.0.0.0:8000->8000/tcp, 0.0.0.0:9000->9000/tcp, 9443/tcp   portainer
vagrant@dockerlab:~$
```

![002_portainer](img/002_portainer.PNG)

`admin/adminPORTAINER`

![003_portainer](img/003_portainer.PNG)

While playing with the docker commands I also found a little bug:

- ansible/roles/dockerlab/files/docker-aliases.sh
  - `alias dip='docker inspect --format="{{ .NetworkSettings.IPAddress }}" $(docker ps --latest --quiet)'` => `alias dip='docker inspect --format="{{ .NetworkSettings.Networks.mgmt_net.IPAddress }}" $(docker ps --latest --quiet)'`

You will also need a GitHub repository with a sample application. Create a new Git repository (this can be on your physical system, where Git and access to GitHub is already configured). Some starter code is provided in directory [cicd-sample-app](../dockerlab/cicd-sample-app/).

1. Ensure that Git is configured, e.g. with `git config --global --list` and check that `user.name` and `user.email` are set. If not, make the necessary changes:

    ```console
    git config --global user.name "Bobby Tables"
    git config --global user.email "bobby.tables@student.hogent.be"
    ```

    ```bash
    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25 (main)
    $ git config --global --list | grep user.
    user.name=Benny Clemmens
    user.email=benny.clemmens@student.hogent.be
    ```

2. Copy the starter code from `cicd-sample-app` to some new directory outside this Git repository. Enter the copied directory and initialise it as a Git repository with `git init`. Commit all code (e.g. `git add .; git commit -m "Initial commit of sample application"`).

    ```bash
    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
    $ cp -r cicd-sample-app/ ../../cicd-sample-app-24-25

    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/dockerlab (main)
    $ cd ../../cicd-sample-app-24-25

    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25
    $ git init
    Initialized empty Git repository in C:/DATA/GIT/IA/cicd-sample-app-24-25/.git/

    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25 (main)
    $ git add .

    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25 (main)
    $ git commit -m 'initial commit'
    [main (root-commit) 437b4d1] initial commit
    4 files changed, 48 insertions(+)
    create mode 100644 sample-app.sh
    create mode 100644 sample_app.py
    create mode 100644 static/style.css
    create mode 100644 templates/index.html
    ```

3. On GitHub, create a new **public** repository and record the URL, probably something like `https://github.com/USER/cicd-sample-app/` (with USER your GitHub username).

    ![004_github](img/004_github.PNG)
    `https://github.com/BennyClemmens/cicd-sample-app-24-25`

4. Link your local repository with the one you created on GitHub: `git remote add origin git@github.com:USER/cicd-sample-app.git` (The GitHub page of your repository will show you the exact command needed for this).

    ```bash
    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25 (main)
    $ git remote add origin https://github.com/BennyClemmens/cicd-sample-app-24-25.git
    ```

5. Push the locally committed code to GitHub: `git push -u origin main`. Take extra care on this command and the option main. If you receive an error, read the git error message carefully. Maybe your GitHub account is (still) configured to use master instead of main.

    ```bash
    Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/cicd-sample-app-24-25 (main)
    $ git push -u origin main
    Enumerating objects: 8, done.
    Counting objects: 100% (8/8), done.
    Delta compression using up to 8 threads
    Compressing objects: 100% (5/5), done.
    Writing objects: 100% (8/8), 1.03 KiB | 529.00 KiB/s, done.
    Total 8 (delta 0), reused 0 (delta 0), pack-reused 0
    To https://github.com/BennyClemmens/cicd-sample-app-24-25.git
    * [new branch]      main -> main
    branch 'main' set up to track 'origin/main'.
    ```

## 1.2 Build and verify the sample application

1. Log in to the VM with `vagrant ssh` and go to directory `/vagrant/cicd-sample-app`
2. Build the application using the `sample-app.sh` script. Keep in mind, if the build script is not executable, you should know what to do. Downloading the image may take a while since it's almost 900 MB. After the build is finished, your application should be running as a Docker container.
3. Verify the app by pointing your browser to <http://192.168.56.20:5050/>. You should see the text "You are calling me from 192.168.56.1" with a blue background.
4. Stop the container and remove it.

## 1.3 Download and run the Jenkins Docker image

1. Download the Jenkins image with `docker pull jenkins/jenkins:lts`
2. Start the Jenkins Docker container:

    ```console
    docker run -p 8080:8080 -u root \
      -v jenkins-data:/var/jenkins_home \
      -v $(which docker):/usr/bin/docker \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v "$HOME":/home \
      --name jenkins_server jenkins/jenkins:lts
    ```

    - Port 8080 is exposed and forwarded to the host system
    - `-u` runs the container as root
    - The first `-v` option mounts a volume for keeping persistent data
    - The second and third `-v` makes the Docker command available inside the Jenkins container. It is necessary to run the container as root to make this work (see the `-u` option).
    - The last line specifies a name for the container and the image to be used
3. The container is started in the foreground. It will emit a password for the admin user generated at random. Record this password somewhere, because remembering will be impossible for most people. If you do forget the password, you can retrieve it from a specific file inside the container with the command `docker exec -it jenkins_server /bin/cat /var/jenkins_home/secrets/initialAdminPassword`)

## 1.4 Configure Jenkins

1. Open a browser tab and point it to <http://192.168.56.20:8080/>. You are asked to enter the administrator password that you recorded in the previous step. Next, Jenkins will ask which plugins you want to have installed. Choose to install the recommended plugins. After this, Jenkins will initialize, which takes some time. You can follow the progress on the web page.
2. When the initialization process finishes, you are redirected to a page that asks you to create an admin user. For now, you can skip this a continued as admin by following the link at the bottom.
3. On the next page, titled "Instance Configuration", just click "Save and Finish" and then "Start using Jenkins".

## 1.5 Use Jenkins to build your application

1. On the Jenkins dashboard, click "Create a new job". Enter a suitable name, e.g. *BuildSampleApp*. Select a "free style project" as job type.
2. The following page allows you to configure the new job. There are a lot of options, so you may be overwhelmed at first.
    - Optionally, enter a description
    - In the section "Source Code Management", select the radio button "Git" and enter the https-URL to your GitHub project, `https://github.com/USER/cicd-sample-app.git`
    - Since your repository is public it is not necessary to enter credentials.
    - The branch to be built should be `*/main` instead of the default `*/master`
    - In the section "Build Steps", click "Add a build step" and select "Execute shell" from the dropdown list. enter `bash ./sample-app.sh`
    - Click "Save". You are redirected to the Jenkins dashboard
3. The dashboard shows an overview of all build jobs. Click the job you just created and in the menu on the left, start a new build job (*"Build Now"*).
    - Hopefully, the build succeeds. Use the overview of build attempts to view the console output of the build process to. If the build process failed this is where you can find error messages that can help to determine the cause.
4. Ensure the sample application is running by reloading the appropriate browser tab.

Take some time to realise what you did here, because it's actually quite cool! We launched Jenkins in a Docker container, and the result of the build job is another container that runs alongside it! The specific options when we launched the Jenkins container make sure that this is possible.

If you manage a Jenkins instance for a larger development team, you will probably want to install Jenkins on a dedicated build server, either a physical machine, or a full-fledged virtual machine. Consider the Jenkins Docker image as suitable for testing purposes only.

Remark that if you try to run the build job a second time, it will fail. Check the console output to determine the cause!

## 1.6 Add a job to test the application

We will now create another job that runs an acceptance test after the build process has finished.

Before you begin, you need to know the IP address of both the `sampleapp` and `jenkins_server` container. Find the IP address and ensure that the app is available with `curl http://APP_IP:5050/`. The output should contain a line with the client's IP address:

```text
   <h1>You are calling me from 172.17.0.1</h1>
```

Remark that 172.17.0.1 is the IP address of the Docker host, i.e. your `dockerlab` VM. Our acceptance test will consist of running the curl command from the Jenkins server, which will have a different IP address.

1. On the Jenkins dashboard, click "Create a new job". Enter a suitable name, e.g. *TestSampleApp*. Select a "free style project" as job type. Optionally, add a description.
2. Under section "Build Triggers", select checkbox "Build after other projects are built". In the text field "Projects to watch", enter the name of the build job.
3. Under section "Build steps", add a build step of type "Execute shell". Enter the following code:

    ```bash
    curl http://APP_IP:5050/ | grep "You are calling me from JENKINS_IP"
    ```

    replacing `APP_IP` and `JENKINS_IP` with the appropriate IP addresses.
4. Save and run the job to verify if it succeeds

    Jenkins can determine whether the job succeeded or failed using the exit status of the command given. When `grep` finds a matching line in the standard output of `curl`, it will finish with exit status 0 (indicating success). If not, it will have exit status 1 (indicating failure). If the command returns a nonzero exit status, it will consider the job to be failed.

    Remark that this is not exactly a full-fledged acceptance test. In a real-life application, you would probably launch a test suite that has to be installed on the Jenkins server.

    You could write a bash script that's a bit more useful than the command specified above. For example, if the job fails, the console output will not give you any clue as to why. In case of a failure to find the expected IP address in the output of `curl`, you could print the actual output on the console.

5. The Jenkins dashboard should now list both the build and test job. Stop and remove the `samplerunning` container and then launch the build job. If you encounter an error try figuring out what is going on. There are multiple ways to tackle this issue either in Jenkins with a correct configuration option or by carefully improving the sample-app.sh script.

## 1.7 Create a build pipeline

The build process in a real-life application is usually much more complex. A full-fledged Continuous Integration/Delivery (CI/CD) pipeline will usually consist of more steps than the ones discussed here (e.g. linting, static code analysis, unit tests, integration tests, acceptance tests, performance tests, packaging and deployment in a production environment). This lab assignment, probably your first encounter with a CI/CD tool is a bit simpler, but should give you an idea of what's possible.

In the next step, we will set up a complete build pipeline that, if the build and test steps succeed, will launch your application as a Docker container.

1. Go to the Jenkins pipeline and create a new item. Enter an appropriate name (e.g. SampleAppPipeline) and select "Pipeline" as job type. Press OK.
2. Optionally, enter a description and in the Pipeline section, enter the following code:

    ```text
    node {
        stage('Preparation') {
            catchError(buildResult: 'SUCCESS') {
                sh 'docker stop samplerunning'
                sh 'docker rm samplerunning'
            }
        }
        stage('Build') {
            build 'BuildSampleApp'
        }
        stage('Results') {
            build 'TestSampleApp'
        }
    }
    ```

    This build pipeline consists of 3 stages:

    - The currently running container is stopped and removed
    - The build job is launched
    - The acceptance job is launched

    Be sure to enter the correct names of the jobs if you have chosen your own names! Finally, save the pipeline.
3. Next, start a build. Jenkins will show you how each phase of the pipeline progresses. Check the console output of each phase.
4. If the run succeeds, the application should be running. Verify by opening it in a web browser.

## 1.7 Use a Jenkinsfile

You can automate the configuration of the build pipeline by adding a `Jenkinsfile` to the root of the application Git repository. For this case, you can use the code for the the pipeline definition in the previous step. Add the jenkinsfile to the root of the Git repository, remove the pipeline from the Jenkins dashboard, create a new pipeline for the Git repository and launch it. The pipeline should run as before!

## 1.9 Make a change in the application

In this final step, we will make a change in the application, re-launch the build pipeline and view the result in the browser.

1. Go to your local copy of the Git repository with the sample application. Open file `static/style.css` and change the page background color from "lightsteelblue" into whatever you want.
2. Save the file, commit your changes and push them to GitHub.
3. In the Jenkins dashboard, launch the build pipeline.
4. Reload the application in the web browser, it should have a different background colour now!

## Reflection

This lab assignment was much less complex than a real-life build pipeline, but you were able to see how Jenkins can be used to automatically build, test *and* deploy an application.

What would change in a real-life case:

- In this lab assignment, we installed Jenkins inside a Docker container. In real life, it would probably be installed as a package on a dedicated virtual machine or bare metal.
- The Git repository would probably be maintained on the Jenkins build server, or a dedicated internal server instead of GitHub. That opens the possibility to trigger a Jenkins build on each push to the central Git repo.
- If GitHub is used, the repository is likely to be private. In that case, you have to configure Jenkins, so it has the necessary credentials to download the code from GitHub, an [access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token).
- The build pipeline would probably be much more elaborate, with linting, static code analysis, unit tests, functional, integration, acceptance and performance tests, ...
- Jenkins would probably package the application (if the build succeeded) and upload it to a package repository.
- In this lab, the application is stopped during the build process. This is of course not desirable on a production server. Usually, you would have the application running on multiple web server instances with a load balancer to distribute client requests to each instance. Deploying the application would consist of launching containers with the new version of the code, and removing those with the old version.
- Depending on the situation, it may be decided that the deployment phase is never done automatically, but manually after a successful build. This is the difference between *Continuous Integration* (no automatic deployment) and *Continuous Delivery*.

And we haven't even discussed any necessary changes to a database schema when new code is deployed!

## Possible extensions

- Create a build pipeline for a larger application, e.g. this [todo list app](https://docs.microsoft.com/en-us/visualstudio/docker/tutorials/your-application)
- Upgrade your pipeline to a *Continuous Delivery/Deployment* pipeline. Try to configure the task(s) in such a way that a push to main would result in automatically deploying a new version of the static website. In other words no interaction on the jenkins server should be needed anymore to trigger the tasks/jobs.
