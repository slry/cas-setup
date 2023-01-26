# Compositional Agile System

## Introduction
Setup of the development environment used for the project of the Software Engineering course at the University of Bologna (A.Y. 2022-2023).

### Description
Compositional Agile System (CAS) in an open source development environment mainly oriented towards the Agile methodology.\
Included software are [Gitlab](https://about.gitlab.com/), [Taiga](https://www.taiga.io/), [Jenkins](https://www.jenkins.io/), [Sonarqube](https://www.sonarsource.com/products/sonarqube/) and [Mattermost](https://mattermost.com/).



## Installation

### Prerequisites
- A Debian server with SSH and root access
- A machine with Ansible (may also be the server itself)
  ```
  # To install Ansible
  pip install --user ansible

  # To install Ansible's Docker module
  ansible-galaxy collection install community.docker
  ```

### Settings
The `hosts` file contains some parameters that have to be changed.
- `ansible_host` set to the address of the server
- `ansible_user` set to the user to use during the installation (has to be root or a sudoer)
- `domain` to the domain name or the IP address of the server

#### Enable HTTPS
If `schema` is set to `https`, it will handle the request of a Let's Encrypt certificate (which will automatically accept its [TOS](https://letsencrypt.org/repository/)) and create a cron job for the renewal.\
In order to issue the certificate, the `email` field is required and the server must be publicly reachable on port 80.



### Deployment
```
ansible-playbook main.yml -i hosts
```
If the user requires password authentication, add the `-kK` flag (it will prompt for the password of the user and then for the password to use sudo).

Note: the server's key fingerprint should already be on the Ansible host.



## Initial setup
### Gitlab 
URL: `http(s)://domain/gitlab`\
The `root` user temporary password is located in the container:
```
docker exec cas-gitlab cat /etc/gitlab/initial_root_password
```

### Jenkins 
URL: `http(s)://domain/jenkins`\
The initial password is located in the container:
```
docker exec cas-jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### Sonarqube 
URL: `http(s)://domain/sonarqube`\
The default user is `admin`/`admin`.

Set the `Server base URL` field in **Administration > Configuration > General Settings > General** with the URL of Sonarqube (e.g. http(s)://domain/sonarqube).

### Taiga 
URL: `http(s)://domain/taiga`\
Create an admin user by running:
```
docker exec -it cas-taiga-back python manage.py createsuperuser
```
The admin panel can be found at `http(s)://domain/taiga/admin/` (beware of the final slash).

### Mattermost 
URL: `http(s)://domain/mattermost`\
The first user to signup will automatically become administrator.



## Setup Gitlab SSO
You can create a Gitlab OAuth application in the **Admin Area > Applications** tab.

### Jenkins
1. Install the plugin: **GitLab Authentication**.
2. Go into the **Manage Jenkins > Configure Global Security** tab and select as **Security Realm** `Gitlab Authentication Plugin`.
3. Create a new Gitlab OAuth application with scope `api` and return URI `/jenkins/securityRealm/finishLogin` (e.g. http(s)://domain/jenkins/securityRealm/finishLogin).
4. On the Jenkins panel insert the required data.

### Sonarqube
1. Go into the **Administration > Configuration > General Settings > Authentication > GitLab Authentication** tab.
2. Follow the on-screen instructions.\
    The return URI of the OAuth application is `/sonarqube/oauth2/callback/gitlab` (e.g. http(s)://domain/sonarqube/oauth2/callback/gitlab) and the scopes are `api` and `read_user`.

### Taiga
1. Create a Gitlab OAuth application with return URI `/taiga/login` (e.g. http(s)://domain/taiga/login) and scope `read_user`.
2. Insert client ID and client secret into the Ansible `hosts` file (`taiga_gitlab_clientid` and `taiga_gitlab_clientsecret`).
3. Re-run the Ansible playbook.

### Mattermost
1. In the **System Console**, go into the **Gitlab** tab (under the **Authentication** section).
2. Follow the on-screen instructions.\
  The scope of the OAuth application is `api`.


## Add or remove a service
To add or remove a service, create (or delete) a role and add (or remove) it to `main.yml`.\
Update `roles/nginx/templates/nginx.conf.j2` accordingly.


## Useful integrations
- [Gitlab - Taiga](https://docs.taiga.io/integrations-gitlab.html)
- [Gitlab - Mattermost](https://docs.gitlab.com/ee/user/project/integrations/mattermost.html)
- [Gitlab - Jenkins](https://docs.gitlab.com/ee/integration/jenkins.html)
- [Jenkins - Sonarqube](https://docs.sonarqube.org/9.6/analyzing-source-code/scanners/jenkins-extension-sonarqube/)
- [Jenkins - Mattermost](https://www.jenkins.io/doc/pipeline/steps/mattermost/)
- [Discord - Jenkins](https://plugins.jenkins.io/discord-notifier/)
- [Discord - Gitlab](https://docs.gitlab.com/ee/user/project/integrations/discord_notifications.html)