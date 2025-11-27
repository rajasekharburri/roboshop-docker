FROM node:20
WORKDIR /opt/server
COPY package.json .
COPY *.js .
# this may add extra cache memory
RUN npm install 
ENV MONGO="true" \
    MONGO_URL="mongodb://mongodb:27017/catalogue"
CMD ["node", "server.js"]