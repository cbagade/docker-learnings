#base image, this image exists on docker hub
FROM node

# setting working directory to run further command
WORKDIR /app

# say you are changing only server.js
# if there is any new package installed then running npm install is needed
# just changing server.js , won't need
# now to have better caching of image layers, we should

COPY package.json /app
RUN npm install


# while rebuilding image this will ensure that all above things is cached
# and instructions here after are only re-evaluated
COPY . /app

# adding ARG for build time variable
# the dafult value is also specified as 80
ARG DEFAULT_PORT=80

# the server.js is changed to app.listen(process.env.PORT);
# value is ARG value
ENV PORT $DEFAULT_PORT

# can be replaced with env var
EXPOSE $PORT


# no need for VOLUME when we want to create named volume 
# the docker run command will change for named volume
# VOLUME [ "/app/feedback" ]

CMD ["node","server.js"]

# supplying port as 8000 (default is 80 and we are changing)
# docker build -t cbagade/cl-data-vol:v6 --build-arg DEFAULT_PORT=8000 .
# docker push cbagade/cl-data-vol:v6
# docker run -d -p 3000:8000 --name feedback-app -v feedback:/app/feedback -v "/home/chandra/docker-learnings/data-volumes-0102-starting-setup/feedback:/app/bindvoldir"  --rm cbagade/cl-data-vol:v6