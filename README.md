# netwotkautomation

### Step 1. Clone repository
- First clone the repository in your system with following command
```
cd ~/ && git clone https://github.com/Dhathripravallika/netwotkautomation.git
```

### Step 2. Create ssh key pairs 
- Create ssh key pairs in your system e.g "default" and you will have the following files in current directory default & default.pub
```
cd ~/ && ssh-keygen -C default
cp default ~/.ssh/
```

- Note :- Use the same public key named default.pub while creating servers on cloud and make sure all servers are accessible with the default private key.

- For example :- I have prepared all servers according to assignment and all servers are accessible via kay pair named "default" which i have already added in cloud at citycontrolpanel key pairs section.

#### Edit the following files in your system and enter the content as below, make sure to replace ips as per your enviroment.

##### Local dns setup
- sudo vi /etc/hosts
```
31.12.85.22 bastionET2598
45.114.122.56 devhaproxy
10.6.0.12 devA
10.6.0.13 devB
10.6.0.14 devC
```
##### Bastion host setup

```
rsync -avz ~/.ssh/default bastionET2598:~/.ssh/id_rsa
```
- Edit /etc/hosts file in bastion server and add the same content as we added for local system.

##### SSH jump host setup

- vi ~/.ssh/config
```
StrictHostKeyChecking no

Host devhaproxy bastionET2598
    User ubuntu
    IdentityFile ~/.ssh/default

Host devA
    Hostname 10.6.0.12
    User ubuntu
    IdentityFile ~/.ssh/default
    ProxyCommand ssh -W  %h:%p -q -A bastionET2598

Host devB
    Hostname 10.6.0.13
    User ubuntu
    IdentityFile ~/.ssh/default
    ProxyCommand ssh -W  %h:%p -q -A bastionET2598

Host devC
    Hostname 10.6.0.14
    User ubuntu
    IdentityFile ~/.ssh/default
    ProxyCommand ssh -W  %h:%p -q -A bastionET2598
```

#### Go to the repository directory and make sure the directory tree should be as shown below.
```
├── README.md
├── hosts
├── roles
│   ├── haproxy
│   │   ├── handlers
│   │   │   └── main.yml
│   │   ├── tasks
│   │   │   └── main.yml
│   │   └── templates
│   │       └── haproxy.cfg
│   ├── haproxy-test
│   │   └── tasks
│   │       └── main.yml
│   ├── nginx
│   │   ├── files
│   │   │   └── nginx-default.conf
│   │   ├── handlers
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   └── php
│       ├── files
│       │   └── index.php
│       └── tasks
│           └── main.yml
└── site.yaml
```

### Step 3. Run the ansible playbook 
- Once everything is configured run ansible playbook named site.yaml with the following commands to deploy HAProxy & Nginx web servers.
```
cd ~/netwotkautomation && ansible-playbook -i hosts site.yaml
```
- ansible playbook with take some time to deploy on each server so keep paitence to get it completed.
- You can check the status by refreshing these below urls e.g

http://45.114.122.56/haproxy?stats

http://45.114.122.56/

#### FYI :-
I have noticed two issues

- Nginx default config provided in document shared and i changed the code as below and places updated code inside templates/nginx.conf.
```
Old code | try_files \$uri \$uri/ =404;
New code | try_files $uri $uri/ /index.php;
```

- While installing php package it install apache2 extra so i have installed first php-fpm then php package to get rid of extra packages.

All done.