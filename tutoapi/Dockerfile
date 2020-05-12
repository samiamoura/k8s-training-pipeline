FROM node:8-alpine

# By default, Docker use the root user but it’s a bad practice to run as root:
RUN addgroup -g 1002 -S app
RUN adduser -u 1002 -S -G app app
RUN chown app:app -R /usr/local/ && mkdir /app/ && chown app:app -R /app


USER app

COPY . /app/

WORKDIR /app

RUN npm install
RUN npm install -g --save wait-for-mysql

CMD wait-for-mysql -h $MYSQL_HOST -u $MYSQL_USER -P $MYSQL_PASSWORD -T 60000 && node app.js

