NOTES
------
when a container is started for the first time it will execute files with extenstions .sh and .js that are found in /docker-entrypoint-initdb.d. files will be executed in alphabatical order .js files will be executed by mongosh


practice
----------
3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker build -t mongodb:v1 .
[+] Building 0.7s (7/7) FINISHED                                                                                docker:default
 => [internal] load build definition from Dockerfile                                                                      0.0s
 => => transferring dockerfile: 92B                                                                                       0.0s
 => [internal] load metadata for docker.io/library/mongo:7.0                                                              0.3s
 => [internal] load .dockerignore                                                                                         0.0s
 => => transferring context: 2B                                                                                           0.0s
 => [internal] load build context                                                                                         0.0s
 => => transferring context: 36B                                                                                          0.0s
 => [1/2] FROM docker.io/library/mongo:7.0@sha256:542095b9517562dbbd4dd061a19459168237eb5f26841ac0e56c89ebb2eb4173        0.0s
 => => resolve docker.io/library/mongo:7.0@sha256:542095b9517562dbbd4dd061a19459168237eb5f26841ac0e56c89ebb2eb4173        0.0s
 => CACHED [2/2] COPY *.js /docker-entrypoint-initdb.d.                                                                   0.0s
 => exporting to image                                                                                                    0.1s
 => => exporting layers                                                                                                   0.0s
 => => exporting manifest sha256:86be261482e0c4a2ab0967a0e09102f9093af9181b029dfa6fe3148815a42da4                         0.0s
 => => exporting config sha256:12a370b7f360a8bf71a79df38a0a5339693e8bfbbf37995e82a93a9e9b501755                           0.0s
 => => exporting attestation manifest sha256:5f18173deb81f35a34e5bad8195e355761ca1326d9ef57e10808fc8f64e475ef             0.0s
 => => exporting manifest list sha256:23b436c78c8748d6f2287d71b70ad56cf3c7b4bf07184022ac7efc9a63abffbb                    0.0s
 => => naming to docker.io/library/mongodb:v1                                                                             0.0s
 => => unpacking to docker.io/library/mongodb:v1                                                                          0.0s

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker run -d mongodb:v1
97019cbfc50c17b4fb77bb425b5849c911a1fa05d74e0611ac43143707d61ba9

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS       NAMES
97019cbfc50c   mongodb:v1     "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   27017/tcp   gifted_nightingale
0a7f5b19a00e   b5cddfc06c3c   "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   27017/tcp   great_hugle

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker rm -f fc
Error response from daemon: No such container: fc

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker rm -f 97
97

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS       NAMES
0a7f5b19a00e   b5cddfc06c3c   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   27017/tcp   great_hugle

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git    #name change we use this
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker run -d --name mongodb mongodb:v1
f737494b25fff73a4101fa4856d2b1ac23523d227ec61e4bd87c68500eebd1a7

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS       NAMES
f737494b25ff   mongodb:v1     "docker-entrypoint.s…"   6 seconds ago   Up 6 seconds   27017/tcp   mongodb
0a7f5b19a00e   b5cddfc06c3c   "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   27017/tcp   great_hugle

3.233.234.102 | 172.31.70.87 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-87 ~/roboshop-docker/mongodb ]$ docker exec -it mongodb bash
root@f737494b25ff:/# mongosh
Current Mongosh Log ID: 692d37c3b0c9e9cb269dc29c
Connecting to:          mongodb://127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&appName=mongosh+2.5.9
Using MongoDB:          7.0.26
Using Mongosh:          2.5.9

For mongosh info see: https://www.mongodb.com/docs/mongodb-shell/


To help improve our products, anonymous usage data is collected and sent to MongoDB periodically (https://www.mongodb.com/legal/privacy-policy).
You can opt-out by running the disableTelemetry() command.

------
   The server generated these startup warnings when booting
   2025-12-01T06:36:20.586+00:00: Access control is not enabled for the database. Read and write access to data and configuration is unrestricted
   2025-12-01T06:36:20.587+00:00: Soft rlimits for open file descriptors too low
   2025-12-01T06:36:20.587+00:00: For customers running MongoDB 7.0, we suggest changing the contents of the following sysfsFile
------

test> show dbs
admin   40.00 KiB
config  12.00 KiB
local   40.00 KiB
test> exit
root@f737494b25ff:/# netstat -lntp
bash: netstat: command not found

root@f737494b25ff:/# cat /etc/*release
DISTRIB_ID=Ubuntu  #os
DISTRIB_RELEASE=22.04
DISTRIB_CODENAME=jammy
DISTRIB_DESCRIPTION="Ubuntu 22.04.5 LTS"
PRETTY_NAME="Ubuntu 22.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.5 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

root@f737494b25ff:/# apt update #To install netstat we use this command
Get:1 http://security.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
Get:2 http://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 InRelease [3005 B]
Get:3 http://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0/multiverse amd64 Packages [96.5 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:5 http://security.ubuntu.com/ubuntu jammy-security/main amd64 Packages [3535 kB]
Get:6 http://security.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [1286 kB]
Get:7 http://security.ubuntu.com/ubuntu jammy-security/restricted amd64 Packages [6008 kB]
Get:8 http://security.ubuntu.com/ubuntu jammy-security/multiverse amd64 Packages [60.9 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
Get:10 http://archive.ubuntu.com/ubuntu jammy-backports InRelease [127 kB]
Get:11 http://archive.ubuntu.com/ubuntu jammy/universe amd64 Packages [17.5 MB]
Get:12 http://archive.ubuntu.com/ubuntu jammy/multiverse amd64 Packages [266 kB]
Get:13 http://archive.ubuntu.com/ubuntu jammy/main amd64 Packages [1792 kB]
Get:14 http://archive.ubuntu.com/ubuntu jammy/restricted amd64 Packages [164 kB]
Get:15 http://archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 Packages [69.2 kB]
Get:16 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [3870 kB]
Get:17 http://archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [1592 kB]
Get:18 http://archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [6222 kB]
Get:19 http://archive.ubuntu.com/ubuntu jammy-backports/main amd64 Packages [83.9 kB]
Get:20 http://archive.ubuntu.com/ubuntu jammy-backports/universe amd64 Packages [35.2 kB]
Fetched 43.2 MB in 4s (11.4 MB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
1 package can be upgraded. Run 'apt list --upgradable' to see it.

root@f737494b25ff:/# apt install net-tools
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  net-tools
0 upgraded, 1 newly installed, 0 to remove and 1 not upgraded.
Need to get 204 kB of archives.
After this operation, 819 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 net-tools amd64 1.60+git20181103.0eebece-1ubuntu5.4 [204 kB]
Fetched 204 kB in 0s (429 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package net-tools.
(Reading database ... 6959 files and directories currently installed.)
Preparing to unpack .../net-tools_1.60+git20181103.0eebece-1ubuntu5.4_amd64.deb ...
Unpacking net-tools (1.60+git20181103.0eebece-1ubuntu5.4) ...
Setting up net-tools (1.60+git20181103.0eebece-1ubuntu5.4) ...

root@f737494b25ff:/# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:27017           0.0.0.0:*               LISTEN      -
root@f737494b25ff:/#
