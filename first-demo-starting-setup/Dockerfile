FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.mjs" ]

# docker build -t cbagade/cl-first-prog:v1 .
# docker push cbagade/cl-first-prog:v1
# docker run -p 3000:3000 cbagade/cl-first-prog:v1
# http://localhost:3000/