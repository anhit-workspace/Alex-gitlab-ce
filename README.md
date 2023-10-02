# Install GITLAB-DOCKER-DEBIAN


## 1. Requiments

 - OS: Debian 11
 - Minimum Resources: RAM 4 GB, CPU 4 cores
 - Real Resources: RAM 8GB, CPU 4 cores
 - Services: gitlab-ce:latest(15.8.1), gitlab-runner:latest, postgresql:lastest
## 2. Setup

### Step 1: Install Docker 

* Link: [Reference links Install Docker Engine](https://docs.docker.com/engine/install/debian/)

### Step 2: Install GitLab using Docker Compose

#### Create a docker-compose.yml file 

```
version: '3'
services:
    gitlab:
      image: gitlab/gitlab-ce:15.8.1-ce.0
      hostname: 'localhost'
      ports:
        - '18891:80'
        - '18892:443'
        - '18893:22'
      volumes:
        - /gitlab/config:/etc/gitlab
        - /gitlab/logs:/var/log/gitlab
        - /gitlab/data:/var/opt/gitlab
      restart: always

    git-runner:
      image: gitlab/gitlab-runner:v15.8.1
      restart: always
      links:
        - gitlab

    postgresql:
      image: postgres:15.1
      environment:
        POSTGRES_USER: gitlab
        POSTGRES_PASSWORD: secret
        POSTGRES_DB: gitlabhq_production
      restart: always
      volumes:
        - /gitlab/postgresql/data:/var/lib/postgresql/data
      ports:
        - 5433:5432
```
#### Start GitLab

```
docker compose up -d
```

* Link: [Install Gitlab by Docker compose](https://docs.gitlab.com/ee/install/docker.html)

**NOTE:**
- When run docker-compose.yml make sure you are in the same directory as docker-compose.yml and start GitLab
- User Login: root
- Password: run command 
```
cat /gitlab/config/initital_root_password
```
**NOTE:** This file will be automatically deleted in the first reconfigure run after 24 hours.
**NOTE:** Change ssh Port default of GITLAB in docker-compose.yml file



