Practice
---------

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/cart ]$ cd ../mysql/

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/mysql ]$ docker build -t mysql:v1 .
[+] Building 21.6s (7/7) FINISHED                                                               docker:default
 => [internal] load build definition from dockerfile                                                      0.1s
 => => transferring dockerfile: 125B                                                                      0.0s
 => [internal] load metadata for docker.io/library/mysql:8.0                                              0.4s
 => [internal] load .dockerignore                                                                         0.0s
 => => transferring context: 2B                                                                           0.0s
 => [internal] load build context                                                                         1.6s
 => => transferring context: 64.60MB                                                                      1.1s
 => [1/2] FROM docker.io/library/mysql:8.0@sha256:c7d30101340e026247603b13dba80bf8075b4378aee39d1989a2a  14.2s
 => => resolve docker.io/library/mysql:8.0@sha256:c7d30101340e026247603b13dba80bf8075b4378aee39d1989a2a6  0.1s
 => => sha256:c5065de1ac5292fd648699c663a1d87bde156d4bc6e5321728bec8031aec4552 122B / 122B                0.1s
 => => sha256:8f9e015324be13d859e1cb9cc057b4ffdf343afeac69aa1b94e8aecd2967eb91 5.33kB / 5.33kB            0.1s
 => => sha256:b391ca50ef0af7e47a3ce9d62ecbf2284c44cd88e0c00f4b1e5bc109361c529c 127.89MB / 127.89MB        3.6s
 => => sha256:03482b81b924f28a4b5c55eb155d3ffe5bc12ef16c3ea8af96a373254f84f114 49.91MB / 49.91MB          1.5s
 => => sha256:49f3ee3949612a64c5485b87e88c4986b3dca9ac63d12c711fdb238d285755b5 331B / 331B                0.1s
 => => sha256:9d9a8a054444545f3860b800e5d4772c600c78a935c5e9cbe2f6c74187881f88 317B / 317B                0.1s
 => => sha256:e5a23e1c40841aebf95515a25dbe361f152b5cd3a5d3d48250bcae77cd0ce617 6.17MB / 6.17MB            0.4s
 => => sha256:c00ba22cf5fa74c55db52e964b0ac26487b83029fba1a728245ecc5ca10faf18 2.61kB / 2.61kB            0.1s
 => => sha256:d46afab7cd73c9a0d356d76775b76f60ca97443bb66d9ae48d85b315d4de5256 783.56kB / 783.56kB        0.1s
 => => sha256:3e71d99754e7fce47a96b019d5b015fa82b14654e2b317def988640790577816 885B / 885B                0.0s
 => => sha256:04a32ca23735c402e5cc07bceb8fa7d06ed2ca51d31dfc4e50593de0945b03dd 47.31MB / 47.31MB          1.3s
 => => extracting sha256:04a32ca23735c402e5cc07bceb8fa7d06ed2ca51d31dfc4e50593de0945b03dd                 2.3s
 => => extracting sha256:3e71d99754e7fce47a96b019d5b015fa82b14654e2b317def988640790577816                 0.0s
 => => extracting sha256:d46afab7cd73c9a0d356d76775b76f60ca97443bb66d9ae48d85b315d4de5256                 0.0s
 => => extracting sha256:e5a23e1c40841aebf95515a25dbe361f152b5cd3a5d3d48250bcae77cd0ce617                 0.2s
 => => extracting sha256:c00ba22cf5fa74c55db52e964b0ac26487b83029fba1a728245ecc5ca10faf18                 0.0s
 => => extracting sha256:49f3ee3949612a64c5485b87e88c4986b3dca9ac63d12c711fdb238d285755b5                 0.0s
 => => extracting sha256:03482b81b924f28a4b5c55eb155d3ffe5bc12ef16c3ea8af96a373254f84f114                 1.3s
 => => extracting sha256:9d9a8a054444545f3860b800e5d4772c600c78a935c5e9cbe2f6c74187881f88                 0.0s
 => => extracting sha256:b391ca50ef0af7e47a3ce9d62ecbf2284c44cd88e0c00f4b1e5bc109361c529c                 7.6s
 => => extracting sha256:8f9e015324be13d859e1cb9cc057b4ffdf343afeac69aa1b94e8aecd2967eb91                 0.0s
 => => extracting sha256:c5065de1ac5292fd648699c663a1d87bde156d4bc6e5321728bec8031aec4552                 0.0s
 => [2/2] COPY db/ /docker-entrypoint-initdb.d                                                            0.4s
 => exporting to image                                                                                    5.8s
 => => exporting layers                                                                                   5.1s
 => => exporting manifest sha256:3c1b65e196cfde16badf5054546b53c8578f24c993469745a511c15dcae34853         0.0s
 => => exporting config sha256:61afc75e1c4b66fe7823cd4a72e39651a995363494c1bc4167b1ce7fc5b64a5b           0.0s
 => => exporting attestation manifest sha256:42ec44c7bedb60f0777efda852c65b00f033e72619fa82b710832476d50  0.0s
 => => exporting manifest list sha256:4c1f5b2fa011e3cdb9a3870b73cd04167c03af3322489ef24091d91eff725bb6    0.0s
 => => naming to docker.io/library/mysql:v1                                                               0.0s
 => => unpacking to docker.io/library/mysql:v1                                                            0.5s

 1 warning found (use docker --debug to expand):
 - SecretsUsedInArgOrEnv: Do not use ARG or ENV instructions for sensitive data (ENV "MYSQL_ROOT_PASSWORD") (line 2)



100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/mysql ]$ docker run -d --name mysql --network roboshop mysql:v1
2ccb3d6b443c7d9b9a2b3b3c82d9a146031d69b92d1e183c16b5a5a300fe3b17

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/mysql ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS             PORTS                 NAMES
2ccb3d6b443c   mysql:v1       "docker-entrypoint.s…"   14 seconds ago      Up 13 seconds      3306/tcp, 33060/tcp   mysql
3fea4b4bac9b   cart:v1        "docker-entrypoint.s…"   58 minutes ago      Up 58 minutes      8080/tcp              cart
ff54a3bfaf20   redis:7        "docker-entrypoint.s…"   About an hour ago   Up About an hour   6379/tcp              redis
d5e0b1fdee07   user:v1        "docker-entrypoint.s…"   About an hour ago   Up About an hour   8080/tcp              user
996ac7deb540   catalogue:v1   "docker-entrypoint.s…"   2 hours ago         Up 2 hours                               catalogue
6a23431adf72   mongodb:v1     "docker-entrypoint.s…"   2 hours ago         Up 2 hours         27017/tcp             mongodb


100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/mysql ]$ docker exec -it mysql bash
bash-5.1# mysql
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
bash-5.1# mysql -u root -pRoboShop@1
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.44 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| cities             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.10 sec)

mysql> exit
Bye
bash-5.1# exit
exit

