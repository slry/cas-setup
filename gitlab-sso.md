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

### Taiga [Obsolete]
1. Create a Gitlab OAuth application with return URI `/taiga/login` (e.g. http(s)://domain/taiga/login) and scope `read_user`.
2. Insert client ID and client secret into the Ansible `hosts` file (`taiga_gitlab_clientid` and `taiga_gitlab_clientsecret`).
3. Re-run the Ansible playbook.

### Mattermost
1. In the **System Console**, go into the **Gitlab** tab (under the **Authentication** section).
2. Follow the on-screen instructions.\
  The scope of the OAuth application is `api`.
