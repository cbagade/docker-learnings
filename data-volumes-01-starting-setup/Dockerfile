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


EXPOSE 80
CMD ["node","server.js"]

# docker build -t cbagade/cl-data-vol:v1 .
# docker run -p 3000:80 -d --name feedback-app --rm cbagade/cl-data-vol:v1
# http://localhost:3000/
# When form is displayed, enter chandra in Title input box and some text in Document Text
# After Save button is clicked, navigate to
# http://localhost:3000/feedback/chandra.txt
# now stop the container
# docker stop feedback-app
# run again
# docker run -p 3000:80 -d --name feedback-app --rm cbagade/cl-data-vol:v1
# now hit
# http://localhost:3000/feedback/chandra.txt
# data is gone
# data was available till container was running
