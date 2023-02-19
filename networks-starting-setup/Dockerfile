FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

CMD ["node", "app.js"]

# app_1.js , mongo code is commented
# build Dockerfile
# docker build -t cbagade/cl-networking:v1 .
# docker run -p 3000:3000 -d --name network-app --rm cbagade/cl-networking:v1
# on postman
# http://localhost:3000/movies
# http://localhost:3000/people
# shows OOB container to outside world communication works


# docker stop network-app
# app_2.js mongo code need to be uncommented
# prior that lets run mongo container
# docker run -d --name mongodb mongo
# see mongo container running
# docker ps -a
# docker inspect mongodb
# look for "NetworkSettings" -> "IPAddress"
# change app_2.js for IP
# docker build -t cbagade/cl-networking:v2 .
# docker run -p 3000:3000 -d --name network-app --rm cbagade/cl-networking:v2
# GET - http://localhost:3000/favorites
# POST - http://localhost:3000/favorites
# {
#    "name":"A new hope",
#    "type":"movie",
#    "url":"http://anything"
#}
# GET - http://localhost:3000/favorites