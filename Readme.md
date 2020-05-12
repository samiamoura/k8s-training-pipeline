# Project to training with DevOps 

Project contains 2 parts :
* a mysql database. On `bdd` folder, you can find a sql file to init your database
* an nodejs api part. It listen on port 3000



## launch the project manually

### launch database part

run a mysql database on your environment.

To add data on mysql database, you can type 
```
mysql -u root -p
```

And copy-past the content of `init-db.sql` file.

### launch api part

Install node and npm on your environment

Modify this part of `app.js` file with your database credential :  

```
export MYSQL_USER=mysqluser;
export MYSQL_HOST=mysqlhost;
export MYSQL_PASSWORD=mysqlpwd;
export MYSQL_DATABASE=workshop;
```

and run the api with
```
npm install
npm run start
```

## launch with docker

```
cd bdd
docker build -t tutobdd .

cd ../tutoapi
npm install
docker build -t tutoapi.

# with docker run 
docker run --name tutomysql -d -v /Users/myname/myfappfolder/mysqldata/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root tutobdd 
docker run --name tutoapi -d -p 3001:3000 --link tutomysql:mysql-container -e MYSQL_HOST=mysql-container -e MYSQL_USER=root -e MYSQL_PASSWORD=root tutoapi


# with docker-compose
docker-compose up -d
```

See url http://localhost:3001/users and http://localhost:3001/


# Add this project on Jenkins

See [here](jenkins)

# Run this project on Kubernetes

See [here](k8s)
