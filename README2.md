# Install GITLAB-DOCKER-DEBIAN


## 1. Requiments

 - OS: Debian 11
 - Minimum Resources: RAM 4 GB, CPU 4 cores
 - Real Resources: RAM 8GB, CPU 4 cores
 - Services: gitlab-ce:latest(15.8.1), gitlab-runner:latest, postgresql:lastest
## 2. Setup

### Step 1: Install sudo tool on host machine

#### Login into host machine then switch to root update and install
```
su -
apt update
apt install sudo
```
#### Fix user is not in the sudoers file in Linux by Edit the sudoers file using the vi editor

```
visudo
```

#### Then Add line *<your username> ALL=(ALL) ALL* in visudo file
#### If type sudo command without password edit: *<your username> ALL=(ALL) NOPASSWD:ALL*

[Reference links edit visudo](https://nudesystems.com/fix-user-is-not-in-the-sudoers-file-in-linux/)


### Step 2: Set-timezone

#### Checking the Current Time Zone

```
timedatectl

```
##### Then change timezone of your location *sudo timedatectl set-timezone <your_time_zone>*
```
timedatectl list-timezones
sudo timedatectl set-timezone Asia/Ho_Chi_Minh
```
[Reference links set-timezone](https://linuxize.com/post/how-to-set-or-change-timezone-in-linux/)

### Step 3: Install Docker 
#### Uninstall old versions
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
#### Set up the repository
```
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
#### Add Docker’s official GPG key:
```
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### Use the following command to set up the repository:
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
#### Install Docker 
```
sudo apt-get update
sudo chmod a+r /etc/apt/keyrings/docker.gpg
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

```
[Reference links Install Docker Engine](https://docs.docker.com/engine/install/debian/)

### Step 4: Manage Docker as a non-root user

#### Create the docker group
```
sudo groupadd docker
```
#### Add your user to the docker group.
```
sudo usermod -aG docker $USER
newgrp docker

```
[Reference links Manage Docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/)

### Step 5: Install GitLab using Docker Compose

#### Create a docker-compose.yml file 
```
version: '3'
services:
    gitlab:
      image: gitlab/gitlab-ce:latest
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
      image: gitlab/gitlab-runner:latest
      restart: always
      links:
        - gitlab

    postgresql:
      image: postgres:latest
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

[Install Gitlab by Docker compose](https://docs.gitlab.com/ee/install/docker.html)
