#base image, this image exists on docker hub
FROM node

# setting working directory to run further command
WORKDIR /app

# which files should go in image
# first . suggest enitre things to be copied from host machine at level od Docker file
# second . suggest , where to copy inside container (/,) (without WORKDIR command)
#
# COPY . .
#
# lets not copy in /
COPY . /app
# with WORKDIR this can be written as 
#
# COPY . ./
#

# this will give instruction to run command, which is npm install in this case
# problem is if this command run without setting WORKDIR, then will run at root (/)
# but we want this to be run inside /app
# WORKDIR is set to /app
RUN npm install

# start server
# following command is incorrect because this will be executed when image is being build
# image is not something which we want to run
# image should be blueprint
#
# RUN node server.js
#
# start server only when container is started from image
#
# CMD node server.js
#
# but CMD is run some different format
#
# CMD ["node","server.js"]
#

# the application listens on port 80
# yet container is not exposing the port 
# but this should be written before CMD instructions
# so finally
# maps 80 port of container to application port
EXPOSE 80
CMD ["node","server.js"]

# docker build -t cbagade/cl-node-app:v1 .
# docker push cbagade/cl-node-app:v1
# map port 80 of host 3000
# docker run -p 3000:80 cbagade/cl-node-app:v1
# docker run -d -p 3000:80 cbagade/cl-node-app:v1 , in detached mode
# http://localhost:3000/