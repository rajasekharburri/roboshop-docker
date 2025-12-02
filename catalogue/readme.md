✅ **STEP-BY-STEP EXPLANATION

(How you solved MongoDB connection issue)**

1️⃣ You built the catalogue Docker image
docker build -t catalogue:v1 .


This created an image that runs server.js and expects a MongoDB connection at:

MONGO_URL=mongodb://mongodb:27017/catalogue


➡️ Important:
This means the catalogue service will try to connect using DNS name mongodb.

2️⃣ You ran both containers — but on the default bridge network
docker run -d --name mongodb mongodb:v1
docker run -d --name catalogue catalogue:v1


Problem:
Default Docker bridge network has no DNS resolution for container names.

Meaning:

catalogue tries to connect to hostname mongodb

But in bridge network, that hostname does not resolve

So connection fails

📌 Your logs confirmed this:

MongoNetworkError


And health check showed:

"mongo": false

3️⃣ You checked Docker networks
docker network ls


You saw only:

bridge

host

none

No custom network.

4️⃣ You created a new custom network
docker network create roboshop


🔥 This is the key step.

Why?

➡️ Custom Docker bridge networks automatically give DNS resolution.
➡️ Containers can reach each other using container names as hostnames.

5️⃣ You removed the old containers
docker rm -f catalogue
docker rm -f mongodb


Because you must start them again attached to the custom network.

6️⃣ You ran MongoDB inside the new network
docker run -d --name mongodb --network roboshop mongodb:v1


This gives MongoDB the hostname:

mongodb.roboshop


(or simply mongodb inside same network)

7️⃣ You ran catalogue inside the new network
docker run -d --name catalogue --network roboshop catalogue:v1


Now the environment variable inside catalogue:

MONGO_URL=mongodb://mongodb:27017/catalogue


finally works because:

✔ DNS entry mongodb now resolves
✔ Both containers are inside same roboshop network

8️⃣ Health check shows success

Inside the container:

curl http://localhost:8080/health


Output:

{"app":"OK","mongo":true}


🎉 MongoDB connection successful!

🌟 FINAL RESULT

Your final working setup:

Custom Docker network: roboshop

MongoDB container connected to roboshop

Catalogue container connected to roboshop

Catalogue service can now resolve hostname mongodb

Connection successful

✅ Why this works

Docker default bridge network
❌ NO automatic DNS
❌ Containers cannot talk using names

Custom docker network (roboshop)
✔ Automatic DNS
✔ Container names become hostnames
✔ catalogue → mongodb connection works

🎯 Summary in 3 Lines

Your app expects MongoDB at hostname mongodb.

Default Docker network does NOT support container name DNS.

Creating a custom network fixed the connectivity.


100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker ]$ cd catalogue/

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ ls
Dockerfile  package.json  readme.md  server.js

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker build -t catalogue:v1 .
[+] Building 62.6s (10/10) FINISHED                                                             docker:default
 => [internal] load build definition from Dockerfile                                                      0.1s
 => => transferring dockerfile: 308B                                                                      0.0s
 => [internal] load metadata for docker.io/library/node:20                                                0.5s
 => [internal] load .dockerignore                                                                         0.0s
 => => transferring context: 2B                                                                           0.0s
 => [1/5] FROM docker.io/library/node:20@sha256:66d2eb8b463114d1f416d61dbd5fa9cea83e8fc250feb9973384677  17.5s
 => => resolve docker.io/library/node:20@sha256:66d2eb8b463114d1f416d61dbd5fa9cea83e8fc250feb99733846772  0.0s
 => => sha256:895878b2e74c8e0e0a2cd1db585e46390697432b15f4b8ee087814f939115a26 449B / 449B                0.1s
 => => sha256:3f3086dc4794e75936310457d642857d1b103ddecb3c11b4d08aa5765b5b6d5f 3.33kB / 3.33kB            0.1s
 => => sha256:cab17b1d52faa70a441da1bbc6884ba494bb232114491ff4f9125c4dd3922a7f 48.41MB / 48.41MB          1.5s
 => => sha256:7785152ed412f78b7125808272a63f5c86ba9a41a0512ae0e690234c905a077a 1.25MB / 1.25MB            0.2s
 => => sha256:708274aafe49b02dddc66f97a5c45bb0b8fcf481ce6b43785b11f287fd4e4e1b 48.48MB / 48.48MB          1.5s
 => => sha256:078b2eece9b24f617524f986db4dd04f977e3e7d6fe15a9088a584147bc6ba05 64.40MB / 64.40MB          1.8s
 => => sha256:a1208d53eb0667932469017a5ef3960e5ed2aea9143d62b983abe2f8593eeb9a 211.46MB / 211.46MB        5.6s
 => => extracting sha256:708274aafe49b02dddc66f97a5c45bb0b8fcf481ce6b43785b11f287fd4e4e1b                 3.0s
 => => sha256:8cdff261ed5cee6fd4e729e68c2831a0abc6c7c017569ab45dfd2240bcc3712d 24.03MB / 24.03MB          0.7s
 => => extracting sha256:8cdff261ed5cee6fd4e729e68c2831a0abc6c7c017569ab45dfd2240bcc3712d                 0.9s
 => => extracting sha256:078b2eece9b24f617524f986db4dd04f977e3e7d6fe15a9088a584147bc6ba05                 2.4s
 => => extracting sha256:a1208d53eb0667932469017a5ef3960e5ed2aea9143d62b983abe2f8593eeb9a                 6.9s
 => => extracting sha256:3f3086dc4794e75936310457d642857d1b103ddecb3c11b4d08aa5765b5b6d5f                 0.1s
 => => extracting sha256:cab17b1d52faa70a441da1bbc6884ba494bb232114491ff4f9125c4dd3922a7f                 2.3s
 => => extracting sha256:7785152ed412f78b7125808272a63f5c86ba9a41a0512ae0e690234c905a077a                 0.1s
 => => extracting sha256:895878b2e74c8e0e0a2cd1db585e46390697432b15f4b8ee087814f939115a26                 0.0s
 => [internal] load build context                                                                         0.0s
 => => transferring context: 5.91kB                                                                       0.0s
 => [2/5] WORKDIR /opt/server                                                                             0.2s
 => [3/5] COPY package.json .                                                                             0.1s
 => [4/5] COPY *.js .                                                                                     0.1s
 => [5/5] RUN npm install                                                                                30.4s
 => exporting to image                                                                                   12.4s
 => => exporting layers                                                                                   9.4s
 => => exporting manifest sha256:ed20cde8f201612ef599b6f90fa25756c77844f33b97726bf9ad0403e4162ff1         0.1s
 => => exporting config sha256:71125be1daacd01fe84ff707aa5ef62ea203edc294a60a56f3a9f53f7af9e951           0.0s
 => => exporting attestation manifest sha256:21f96c902c164e29a2beaacaf4b9716ec2803f50bc06d0941e89fe8573b  0.0s
 => => exporting manifest list sha256:fb513e51611b5331ea3b15b730af77bb49e6f4a76ffffbd303aa075cbf5481d5    0.0s
 => => naming to docker.io/library/catalogue:v1                                                           0.0s
 => => unpacking to docker.io/library/catalogue:v1                                                        2.6s

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker images
                                                                                           i Info →   U  In Use
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
catalogue:v1   fb513e51611b       1.69GB          420MB
mongodb:v1     2267e7020679       1.13GB          280MB    U

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker run -d --name catalogue catalogue:v1
91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker exec -it catalogue  bash
root@91565f6a2406:/opt/server# curl http://localhost:8080/health
{"app":"OK","mongo":false}root@91565f6a2406:/opt/server# exit
exit

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker logs catalogue
(node:1) [MONGODB DRIVER] Warning: Current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
(Use `node --trace-warnings ...` to show where the warning was created)
{"level":"info","time":1764649283652,"pid":1,"hostname":"91565f6a2406","msg":"Started on port 8080","v":1}
{"level":"error","time":1764649283657,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649285661,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649287666,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649289670,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649291674,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649293676,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649295680,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649297685,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649299690,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649301694,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649303696,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649305700,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649307704,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649309708,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649311712,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649313714,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649315718,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649317722,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649319726,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649321732,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649323735,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649325739,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649327743,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649329746,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649331750,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649333751,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649335755,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649337759,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649339763,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649341767,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649343769,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649345773,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649347777,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649349781,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649351789,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649353791,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649355793,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649357796,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649359801,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649361805,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649363807,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649365811,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649367815,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649369819,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649371823,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649373825,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649375829,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649377833,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649379838,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649381842,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649383843,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649385847,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649387851,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649389855,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649391858,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649393859,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649395863,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649397867,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649399870,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649401874,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649403876,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649405879,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649407883,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649409888,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649411892,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649413894,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649415896,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649417900,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649419904,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649421908,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649423910,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649425914,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649427920,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649429924,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649431929,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649433930,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649435934,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649437937,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649439941,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649441947,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649443948,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649445952,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649447956,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649449960,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649451965,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649453967,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649455969,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649457973,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649459977,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649461981,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649463984,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649465988,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649467990,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649469994,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649472003,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649474005,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649476009,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649478012,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649480016,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649482018,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649484020,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649486023,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649488027,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649490031,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649492034,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649494040,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649496043,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649498047,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649500061,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649502066,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649504070,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649506074,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649508078,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649510082,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649512089,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649514091,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649516094,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649518098,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649520101,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649522106,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649524108,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"info","time":1764649524618,"pid":1,"hostname":"91565f6a2406","req":{"id":1,"method":"GET","url":"/health","headers":{"host":"localhost:8080","user-agent":"curl/7.88.1","accept":"*/*"},"remoteAddress":"::ffff:127.0.0.1","remotePort":57930},"res":{"statusCode":200,"headers":{"x-powered-by":"Express","timing-allow-origin":"*","access-control-allow-origin":"*","content-type":"application/json; charset=utf-8","content-length":"26","etag":"W/\"1a-Rqd1uvgAuP2oH3KU5F8ibKMS3po\""}},"responseTime":15,"msg":"request completed","v":1}
{"level":"error","time":1764649526111,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649528115,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649530119,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649532122,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649534123,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649536127,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649538131,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649540135,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649542137,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}
{"level":"error","time":1764649544139,"pid":1,"hostname":"91565f6a2406","msg":"ERROR {\"name\":\"MongoNetworkError\"}","v":1}

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ ifconfig
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::cc90:6dff:fea8:2a81  prefixlen 64  scopeid 0x20<link>
        ether ce:90:6d:a8:2a:81  txqueuelen 0  (Ethernet)
        RX packets 8157  bytes 611113 (596.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 9141  bytes 61942406 (59.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.70.233  netmask 255.255.240.0  broadcast 172.31.79.255
        inet6 fe80::14ff:d4ff:fe66:403f  prefixlen 64  scopeid 0x20<link>
        ether 16:ff:d4:66:40:3f  txqueuelen 1000  (Ethernet)
        RX packets 378897  bytes 1068146733 (1018.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 64332  bytes 5170721 (4.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth5e9f286: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::b4d5:dcff:fe36:3810  prefixlen 64  scopeid 0x20<link>
        ether b6:d5:dc:36:38:10  txqueuelen 0  (Ethernet)
        RX packets 3  bytes 126 (126.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 31  bytes 2106 (2.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth6fbbada: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::646f:abff:feb6:3c45  prefixlen 64  scopeid 0x20<link>
        ether 66:6f:ab:b6:3c:45  txqueuelen 0  (Ethernet)
        RX packets 308  bytes 22008 (21.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 317  bytes 45738 (44.6 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

vetha52b552: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::4061:e2ff:fe99:487b  prefixlen 64  scopeid 0x20<link>
        ether 42:61:e2:99:48:7b  txqueuelen 0  (Ethernet)
        RX packets 4896  bytes 329163 (321.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5345  bytes 43788148 (41.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker inspect catalogue
[
    {
        "Id": "91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7",
        "Created": "2025-12-02T04:21:22.575473958Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "node",
            "server.js"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4544,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2025-12-02T04:21:22.679795568Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:fb513e51611b5331ea3b15b730af77bb49e6f4a76ffffbd303aa075cbf5481d5",
        "ResolvConfPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/hostname",
        "HostsPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/hosts",
        "LogPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7-json.log",
        "Name": "/catalogue",
        "RestartCount": 0,
        "Driver": "overlayfs",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                58,
                111
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": null,
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/asound",
                "/proc/interrupts",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/sys/devices/virtual/powercap",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "Storage": {
            "RootFS": {
                "Snapshot": {
                    "Name": "overlayfs"
                }
            }
        },
        "Mounts": [],
        "Config": {
            "Hostname": "91565f6a2406",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NODE_VERSION=20.19.6",
                "YARN_VERSION=1.22.22",
                "MONGO=true",
                "MONGO_URL=mongodb://mongodb:27017/catalogue"
            ],
            "Cmd": [
                "node",
                "server.js"
            ],
            "Image": "catalogue:v1",
            "Volumes": null,
            "WorkingDir": "/opt/server",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "Labels": {}
        },
        "NetworkSettings": {
            "SandboxID": "7e1efa7872329899dfded0f733d9716cd431c0c21e731f90fbfe8cdd6da60bae",
            "SandboxKey": "/var/run/docker/netns/7e1efa787232",
            "Ports": {},
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "1e57b46be2b6cc0b2e3d22c3e09c3aa20f055c9de30c1a8477094fdae5fc7d01",
                    "EndpointID": "e18f95217040c5f19831fe36a13f449295eac3ca7a501a3ba39ce6b42dad0221",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.4",
                    "MacAddress": "ce:d5:40:8f:ae:85",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": null
                }
            }
        },
        "ImageManifestDescriptor": {
            "mediaType": "application/vnd.oci.image.manifest.v1+json",
            "digest": "sha256:ed20cde8f201612ef599b6f90fa25756c77844f33b97726bf9ad0403e4162ff1",
            "size": 2582,
            "platform": {
                "architecture": "amd64",
                "os": "linux"
            }
        }
    }
]

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker inspect mongodb
[
    {
        "Id": "e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477",
        "Created": "2025-12-02T04:04:38.969372347Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "mongod"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 3514,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2025-12-02T04:04:39.031324775Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:2267e70206792a6f0ffb3d7444d68ec9caf8ba56831e8969d9a81ef79dd25b9d",
        "ResolvConfPath": "/var/lib/docker/containers/e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477/hostname",
        "HostsPath": "/var/lib/docker/containers/e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477/hosts",
        "LogPath": "/var/lib/docker/containers/e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477/e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477-json.log",
        "Name": "/mongodb",
        "RestartCount": 0,
        "Driver": "overlayfs",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                58,
                111
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": null,
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/asound",
                "/proc/interrupts",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/sys/devices/virtual/powercap",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "Storage": {
            "RootFS": {
                "Snapshot": {
                    "Name": "overlayfs"
                }
            }
        },
        "Mounts": [
            {
                "Type": "volume",
                "Name": "a8284c1eadec018028131f84f641d77e9b98d1b7622ff3aad865efa94dbebe3c",
                "Source": "/var/lib/docker/volumes/a8284c1eadec018028131f84f641d77e9b98d1b7622ff3aad865efa94dbebe3c/_data",
                "Destination": "/data/db",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            },
            {
                "Type": "volume",
                "Name": "2eca92486fb711e456eb46bc31fe9498e4e946107140617b19d16564ee48b64d",
                "Source": "/var/lib/docker/volumes/2eca92486fb711e456eb46bc31fe9498e4e946107140617b19d16564ee48b64d/_data",
                "Destination": "/data/configdb",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ],
        "Config": {
            "Hostname": "e4ea45045ac5",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "27017/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.19",
                "JSYAML_VERSION=3.13.1",
                "JSYAML_CHECKSUM=662e32319bdd378e91f67578e56a34954b0a2e33aca11d70ab9f4826af24b941",
                "MONGO_PACKAGE=mongodb-org",
                "MONGO_REPO=repo.mongodb.org",
                "MONGO_MAJOR=7.0",
                "MONGO_VERSION=7.0.26",
                "HOME=/data/db"
            ],
            "Cmd": [
                "mongod"
            ],
            "Image": "mongodb:v1",
            "Volumes": {
                "/data/configdb": {},
                "/data/db": {}
            },
            "WorkingDir": "",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "NetworkSettings": {
            "SandboxID": "fd0fc054400863dfe4bfe619538005232b842b2d817bba987f152a5c60378f85",
            "SandboxKey": "/var/run/docker/netns/fd0fc0544008",
            "Ports": {
                "27017/tcp": null
            },
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "DriverOpts": null,
                    "GwPriority": 0,
                    "NetworkID": "1e57b46be2b6cc0b2e3d22c3e09c3aa20f055c9de30c1a8477094fdae5fc7d01",
                    "EndpointID": "9da3b8bc0ed500d94f53575a298e34daa4b64045a1923a7e55a2fe1cd8d43d36",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "MacAddress": "ee:3e:b8:95:c4:e6",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": null
                }
            }
        },
        "ImageManifestDescriptor": {
            "mediaType": "application/vnd.oci.image.manifest.v1+json",
            "digest": "sha256:9cc9e0dde8e7e7d4a8bd336d39b3b8d2c85c548255ad43820599163c403080b7",
            "size": 2001,
            "platform": {
                "architecture": "amd64",
                "os": "linux"
            }
        }
    }
]

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network
Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
1e57b46be2b6   bridge    bridge    local
ec641fb87751   host      host      local
90380fca13e1   none      null      local

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker run -d --network host nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
de57a609c9d5: Pull complete
0e4bc2bd6656: Pull complete
53d743880af4: Pull complete
b5feb73171bf: Pull complete
192e2451f875: Pull complete
108ab8292820: Pull complete
77fa2eb06317: Pull complete
43536266b8d9: Download complete
137b08466659: Download complete
Digest: sha256:553f64aecdc31b5bf944521731cd70e35da4faed96b2b7548a3d8e2598c52a42
Status: Downloaded newer image for nginx:latest
9379dacbf67c81e61de02a7d36f99e99f72f1284c608d99dad88ea28df8bef40

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
1e57b46be2b6   bridge    bridge    local
ec641fb87751   host      host      local
90380fca13e1   none      null      local

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker inspect 1e57b46be2b6
[
    {
        "Name": "bridge",
        "Id": "1e57b46be2b6cc0b2e3d22c3e09c3aa20f055c9de30c1a8477094fdae5fc7d01",
        "Created": "2025-12-02T03:59:36.510182532Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv4": true,
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "IPRange": "",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {},
        "Containers": {
            "8365e561f5adc6da9de81a07c1438cff27eed1dd25cf14d6cdabe35d32eb6250": {
                "Name": "flamboyant_khorana",
                "EndpointID": "0a5a21ab08cbc583d4354fe38f38f045329c2028e9483483c973bce6b38aacf9",
                "MacAddress": "6a:d5:74:bf:22:4c",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            },
            "91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7": {
                "Name": "catalogue",
                "EndpointID": "e18f95217040c5f19831fe36a13f449295eac3ca7a501a3ba39ce6b42dad0221",
                "MacAddress": "ce:d5:40:8f:ae:85",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            },
            "e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477": {
                "Name": "mongodb",
                "EndpointID": "9da3b8bc0ed500d94f53575a298e34daa4b64045a1923a7e55a2fe1cd8d43d36",
                "MacAddress": "ee:3e:b8:95:c4:e6",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            }
        },
        "Status": {
            "IPAM": {
                "Subnets": {
                    "172.17.0.0/16": {
                        "IPsInUse": 6,
                        "DynamicIPsAvailable": 65530
                    }
                }
            }
        }
    }
]

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network
Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker create roboshop
Unable to find image 'roboshop:latest' locally
Error response from daemon: pull access denied for roboshop, repository does not exist or may require 'docker login'

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network  create roboshop
6b709a7f9a3981aeadbf86d8c5dad38534c5d73b801f66cae71ae71841817958

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network ls
NETWORK ID     NAME       DRIVER    SCOPE
1e57b46be2b6   bridge     bridge    local
ec641fb87751   host       host      local
90380fca13e1   none       null      local
6b709a7f9a39   roboshop   bridge    local

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network disconnect bridge mongodb

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network ls
NETWORK ID     NAME       DRIVER    SCOPE
1e57b46be2b6   bridge     bridge    local
ec641fb87751   host       host      local
90380fca13e1   none       null      local
6b709a7f9a39   roboshop   bridge    local

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network disconnect bridge catalogue

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network connect bridge catalogue

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker network connect bridge mongodb

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker inspect catalogue
[
    {
        "Id": "91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7",
        "Created": "2025-12-02T04:21:22.575473958Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "node",
            "server.js"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 4544,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2025-12-02T04:21:22.679795568Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:fb513e51611b5331ea3b15b730af77bb49e6f4a76ffffbd303aa075cbf5481d5",
        "ResolvConfPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/hostname",
        "HostsPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/hosts",
        "LogPath": "/var/lib/docker/containers/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7/91565f6a240609160c8ad963e1f69715666b977217a5b805bc31f3d6c6355ba7-json.log",
        "Name": "/catalogue",
        "RestartCount": 0,
        "Driver": "overlayfs",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                58,
                111
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": null,
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/acpi",
                "/proc/asound",
                "/proc/interrupts",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/sys/devices/virtual/powercap",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "Storage": {
            "RootFS": {
                "Snapshot": {
                    "Name": "overlayfs"
                }
            }
        },
        "Mounts": [],
        "Config": {
            "Hostname": "91565f6a2406",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NODE_VERSION=20.19.6",
                "YARN_VERSION=1.22.22",
                "MONGO=true",
                "MONGO_URL=mongodb://mongodb:27017/catalogue"
            ],
            "Cmd": [
                "node",
                "server.js"
            ],
            "Image": "catalogue:v1",
            "Volumes": null,
            "WorkingDir": "/opt/server",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "Labels": {}
        },
        "NetworkSettings": {
            "SandboxID": "7e1efa7872329899dfded0f733d9716cd431c0c21e731f90fbfe8cdd6da60bae",
            "SandboxKey": "/var/run/docker/netns/7e1efa787232",
            "Ports": {},
            "Networks": {
                "bridge": {
                    "IPAMConfig": {
                        "IPv4Address": "",
                        "IPv6Address": ""
                    },
                    "Links": null,
                    "Aliases": [],
                    "DriverOpts": {},
                    "GwPriority": 0,
                    "NetworkID": "1e57b46be2b6cc0b2e3d22c3e09c3aa20f055c9de30c1a8477094fdae5fc7d01",
                    "EndpointID": "e2d44fc0a4e22322fb02345ad3387a8fcfccf6ec7ce84e3acecd7bd5406afda5",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.3",
                    "MacAddress": "e2:a5:a0:99:58:24",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": null
                }
            }
        },
        "ImageManifestDescriptor": {
            "mediaType": "application/vnd.oci.image.manifest.v1+json",
            "digest": "sha256:ed20cde8f201612ef599b6f90fa25756c77844f33b97726bf9ad0403e4162ff1",
            "size": 2582,
            "platform": {
                "architecture": "amd64",
                "os": "linux"
            }
        }
    }
]

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ ipcinfig
-bash: ipcinfig: command not found

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ ipconfig
-bash: ipconfig: command not found

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ ifconfig
br-6b709a7f9a39: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.18.0.1  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 9a:b8:58:ab:3d:e2  txqueuelen 0  (Ethernet)
        RX packets 3  bytes 126 (126.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 39  bytes 2470 (2.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::cc90:6dff:fea8:2a81  prefixlen 64  scopeid 0x20<link>
        ether ce:90:6d:a8:2a:81  txqueuelen 0  (Ethernet)
        RX packets 9827  bytes 707139 (690.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10805  bytes 62185936 (59.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.70.233  netmask 255.255.240.0  broadcast 172.31.79.255
        inet6 fe80::14ff:d4ff:fe66:403f  prefixlen 64  scopeid 0x20<link>
        ether 16:ff:d4:66:40:3f  txqueuelen 1000  (Ethernet)
        RX packets 411550  bytes 1132728531 (1.0 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 73750  bytes 5944925 (5.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth2d57c10: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::a07e:2eff:fed9:87d8  prefixlen 64  scopeid 0x20<link>
        ether a2:7e:2e:d9:87:d8  txqueuelen 0  (Ethernet)
        RX packets 108  bytes 7686 (7.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 118  bytes 16434 (16.0 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth5e9f286: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::b4d5:dcff:fe36:3810  prefixlen 64  scopeid 0x20<link>
        ether b6:d5:dc:36:38:10  txqueuelen 0  (Ethernet)
        RX packets 3  bytes 126 (126.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 39  bytes 2470 (2.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth8ed5db4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::b0db:4ff:fe75:8e14  prefixlen 64  scopeid 0x20<link>
        ether b2:db:04:75:8e:14  txqueuelen 0  (Ethernet)
        RX packets 3  bytes 126 (126.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10  bytes 796 (796.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker exec -it catalogue bash
root@91565f6a2406:/opt/server# curl http://localhost:8080/health
{"app":"OK","mongo":false}root@91565f6a2406:/opt/server# ^C
root@91565f6a2406:/opt/server# exit
exit

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker exec -it catalogue bash
root@91565f6a2406:/opt/server# curl http://localhost:8080/health
root@91565f6a2406:/opt/server# exit
exit

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS             PORTS       NAMES
9379dacbf67c   nginx          "/docker-entrypoint.…"   36 minutes ago      Up 36 minutes                  exciting_poincare
91565f6a2406   catalogue:v1   "docker-entrypoint.s…"   51 minutes ago      Up 51 minutes                  catalogue
e4ea45045ac5   mongodb:v1     "docker-entrypoint.s…"   About an hour ago   Up About an hour   27017/tcp   mongodb
8365e561f5ad   mongodb:v1     "docker-entrypoint.s…"   About an hour ago   Up About an hour   27017/tcp   flamboyant_khorana

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker rm 8365e561f5ad
Error response from daemon: cannot remove container "8365e561f5ad": container is running: stop the container before removing or force remove

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker rm -f 8365e561f5ad
8365e561f5ad

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS             PORTS       NAMES
9379dacbf67c   nginx          "/docker-entrypoint.…"   37 minutes ago      Up 37 minutes                  exciting_poincare
91565f6a2406   catalogue:v1   "docker-entrypoint.s…"   52 minutes ago      Up 52 minutes                  catalogue
e4ea45045ac5   mongodb:v1     "docker-entrypoint.s…"   About an hour ago   Up About an hour   27017/tcp   mongodb

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker exec -it catalogue bash
root@91565f6a2406:/opt/server# curl http://localhost:8080/health
{"app":"OK","mongo":false}root@91565f6a2406:/opt/server# exit
exit

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker run -d --name mongodb mongodb:v1
docker: Error response from daemon: Conflict. The container name "/mongodb" is already in use by container "e4ea45045ac56c04c1baef44c761b356751ba63c4b66cb05506c948653b9e477". You have to remove (or rename) that container to be able to reuse that name.

Run 'docker run --help' for more information

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker rm -f catalogue
docker rm -f mongodb
catalogue
mongodb

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker run -d --name mongodb --network roboshop mongodb:v1
6a23431adf72ccc9f188b094b0779533a655272fb3894e1dd520f182ee3766cd

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker run -d --name catalogue --network roboshop catalogue:v1
996ac7deb5405442a5e2f74c3ffb3b8eb42820e220331f2e3a27b68512524f48

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker exec -it catalogue bash
curl http://localhost:8080/health
root@996ac7deb540:/opt/server# ^C
root@996ac7deb540:/opt/server# curl http://localhost:8080/health
{"app":"OK","mongo":true}root@996ac7deb540:/opt/server# exit
exit
curl: (7) Failed to connect to localhost port 8080: Connection refused

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS       NAMES
996ac7deb540   catalogue:v1   "docker-entrypoint.s…"   About a minute ago   Up About a minute               catalogue
6a23431adf72   mongodb:v1     "docker-entrypoint.s…"   About a minute ago   Up About a minute   27017/tcp   mongodb
9379dacbf67c   nginx          "/docker-entrypoint.…"   42 minutes ago       Up 42 minutes                   exciting_poincare

100.28.219.90 | 172.31.70.233 | t3.micro | https://github.com/rajasekharburri/roboshop-docker.git
[ ec2-user@ip-172-31-70-233 ~/roboshop-docker/catalogue ]$
