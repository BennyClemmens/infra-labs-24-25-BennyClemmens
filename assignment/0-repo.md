# Setting up git

```bash
Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens
$ git init
Initialized empty Git repository in C:/DATA/GIT/IA/infra-labs-24-25-BennyClemmens/.git/

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ mv .gitignore_ .gitignore

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ mv .gitattributes_ .gitattributes

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ cat .gitignore
# .gitignore

# Hidden Vagrant-directory
.vagrant

# Python development
.ropeproject

# Backup files (e.g. Vim, Gedit, etc.)
*~

# Compiled Python
*.pyc

# BATS installation
test/bats/

# Vagrant base boxes (you never know when someone puts one in the repository)
*.box

# Directories containing roles imported from Ansible Galaxy
# (user.role notation)
*/ansible/roles/*.*

# Ansible Retry-files
*.retry

# Ansible fact cache
.ansible_cache/

# Node.js build directory
node_modules/

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ cat .gitattributes
# Default behaviour
* text=auto

# Shell scripts should have Unix endings
*.sh text eol=lf
*.bats text eol=lf
*.py text eol=lf

# Windows Batch or PowerShell scripts should have CRLF endings
*.bat text eol=crlf
*.ps1 text eol=crlf


Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ git add .

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ git commit -m "initial commit, copy of infra-labs on 2024/10/11"
[main (root-commit) 3c22dcb] initial commit, copy of infra-labs on 2024/10/11
 57 files changed, 3479 insertions(+)
 create mode 100644 .gitattributes
 create mode 100644 .gitignore
 create mode 100644 LICENSE.md
 create mode 100644 README.md
 create mode 100644 assignment/1-cicd.md
 create mode 100644 assignment/2-cfgmgmt.md
 create mode 100644 assignment/2.7-router-vyos.md
 create mode 100644 assignment/3-monitoring.md
 create mode 100644 assignment/4-kubernetes.md
 create mode 100644 assignment/README.md
 create mode 100644 assignment/img/1-helloapp.png
 create mode 100644 assignment/img/1-portainer-connect.png
 create mode 100644 assignment/img/1-portainer-create-user.png
 create mode 100644 assignment/img/1-portainer-dashboard.png
 create mode 100644 assignment/img/1-portainer-endpoints.png
 create mode 100644 assignment/img/1-todo-app.png
 create mode 100644 assignment/img/4-website.png
 create mode 100644 dockerlab/.yamllint
 create mode 100644 dockerlab/LICENSE
 create mode 100644 dockerlab/README.md
 create mode 100644 dockerlab/Vagrantfile
 create mode 100644 dockerlab/ansible/group_vars/all.yml
 create mode 100644 dockerlab/ansible/roles/dockerlab/defaults/main.yml
 create mode 100644 dockerlab/ansible/roles/dockerlab/files/docker-aliases.sh
 create mode 100644 dockerlab/ansible/roles/dockerlab/handlers/main.yml
 create mode 100644 dockerlab/ansible/roles/dockerlab/tasks/main.yml
 create mode 100644 dockerlab/ansible/roles/dockerlab/templates/etc_docker_daemon.json
 create mode 100644 dockerlab/ansible/roles/dockerlab/vars/Ubuntu.yml
 create mode 100644 dockerlab/ansible/site.yml
 create mode 100644 dockerlab/cicd-sample-app/sample-app.sh
 create mode 100644 dockerlab/cicd-sample-app/sample_app.py
 create mode 100644 dockerlab/cicd-sample-app/static/style.css
 create mode 100644 dockerlab/cicd-sample-app/templates/index.html
 create mode 100644 dockerlab/custom-vagrant-hosts.yml
 create mode 100644 dockerlab/scripts/inventory.py
 create mode 100644 dockerlab/test/runbats.sh
 create mode 100644 dockerlab/vagrant-groups.yml
 create mode 100644 dockerlab/vagrant-hosts.yml
 create mode 100644 kubernetes/4.2/bootcamp-all.yml
 create mode 100644 kubernetes/4.2/bootcamp-deployment.yml
 create mode 100644 kubernetes/4.2/bootcamp-service.yml
 create mode 100644 kubernetes/4.3/example-pods-with-labels.yml
 create mode 100644 report/README.md
 create mode 100644 report/cheat-sheet.md
 create mode 100644 report/report-template.md
 create mode 100644 vmlab/.yamllint
 create mode 100644 vmlab/LICENSE
 create mode 100644 vmlab/README.md
 create mode 100644 vmlab/Vagrantfile
 create mode 100644 vmlab/ansible/files/db.sql
 create mode 100644 vmlab/ansible/files/test.php
 create mode 100644 vmlab/ansible/group_vars/all.yml
 create mode 100644 vmlab/ansible/site.yml
 create mode 100644 vmlab/scripts/control.sh
 create mode 100644 vmlab/scripts/util.sh
 create mode 100644 vmlab/test/runbats.sh
 create mode 100644 vmlab/vagrant-hosts.yml

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ git branch -M main

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ git remote add origin https://github.com/BennyClemmens/infra-labs-24-25-BennyClemmens.git

Benny@FLAB2021 MINGW64 /c/DATA/GIT/IA/infra-labs-24-25-BennyClemmens (main)
$ git push -u origin main
Enumerating objects: 82, done.
Counting objects: 100% (82/82), done.
Delta compression using up to 8 threads
Compressing objects: 100% (68/68), done.
Writing objects: 100% (82/82), 496.29 KiB | 16.54 MiB/s, done.
Total 82 (delta 5), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (5/5), done.
To https://github.com/BennyClemmens/infra-labs-24-25-BennyClemmens.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```
