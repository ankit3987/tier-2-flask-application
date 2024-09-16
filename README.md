 
# Flask App with MySQL Docker Setup

This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.

## Prerequisites

Before you begin, make sure you have the following installed:

- Docker
- Git (optional, for cloning the repository)

## Setup

1. Clone this repository (if you haven't already):

   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```

2. Navigate to the project directory:

   ```bash
   cd your-repo-name
   ```

Pre-reqiusite:- 
-> docker installed and configured on the system 
### This is without docker-compose 
1. Build flaskapp image from docker file :- 
`
docker build -t flaskapp .
`

2. Create network:-  This is because both backend and front end container will connected with each oth on same network

so in this project we have to create 2 containers one is flask and other is db and we have to connect with each other without compose so what we can do is we create a network and run or create both container in that network

Create network:-
`docker network create twotier`

check network list :-
`docker network ls`

check inside the network :-
`docker network inspect network-name`

Before next step please check that all the configuration are same written in the app.py file please make sure that all the configuration are same if any missconfiguration then it will give an error 

3. Run mysql container :- 
```bash
docker run --name mysql-container --network twotier \  #same network
    -e MYSQL_ROOT_PASSWORD=root \     #this is same pasword in mysql 
    -e MYSQL_DB=devops \
    -e MYSQL_USER=admin \ 
    -e MYSQL_PASSWORD=admin \
    -d mysql:5.7
```
Note :- this after that you may face an error that database is not foud then you have to create database by entring in the container by the name of you provided in the above

4. Run flaskapp container :- Run the application from the images which I build from my dockerfile
```bash
docker run -d \
    --name flaskapp-cont \
    --network=twotier \
    -e MYSQL_HOST=mysql-container \  #this is same name of the mysql conaitner
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=root \     #this is same pasword in mysql conaitner
    -e MYSQL_DB=devops \         #this is mysqldb.it is same in mysql
    -p 5000:5000 \
    flaskapp
```
   1. Open 5000 port on the instance :-
   2. Your application deploy sucessfully:- 

If You want to add or view data from the terminal then firstly You want to interact with mysql conatiner then after that You can run some query
For going inside the terminal:- `docker exec -it mysql-container mysql -uroot -p`
    enter password `root`
  1. list databses:- `SHOW DATABASES;`

  2. Select the Database:- `USE devops;`

  3. List Tables :- `SHOW TABLES;`

  4. Run sql Query :- 
```
SELECT * FROM messages;   #show all the data 

INSERT INTO messages (message) VALUES ('Hello, world!');    #Inserting the data from terminal 

exit            #exit from the Mysql shell 
```
---
## This is with docker-compose

Pre-requisite:-  `sudo apt install docker-compose-v2`
- Note:- Make sure all the configuration is matched with app.py file's configuration
### Usage

1. Start the containers using Docker Compose:

   ```bash
   docker-compose up -d
   ```

2. Access the Flask app in your web browser:

   - Frontend: http://localhost
   - Backend: http://localhost:5000

3. Create the `messages` table in your MySQL database:

   - Use a MySQL client or tool (e.g., phpMyAdmin) to execute the following SQL commands:
   
     ```sql
     CREATE TABLE messages (
         id INT AUTO_INCREMENT PRIMARY KEY,
         message TEXT
     );
     ```

4. Interact with the app:

   - Visit http://localhost to see the frontend. You can submit new messages using the form.
   - Visit http://localhost:5000/insert_sql to insert a message directly into the `messages` table via an SQL query.


5. To down the docker compose:- 
  ```bash
  docker compose down
```

## Notes

- Make sure to replace placeholders (e.g., `your_username`, `your_password`, `your_database`) with your actual MySQL configuration.

- This is a basic setup for demonstration purposes. In a production environment, you should follow best practices for security and performance.

- Be cautious when executing SQL queries directly. Validate and sanitize user inputs to prevent vulnerabilities like SQL injection.

- If you encounter issues, check Docker logs and error messages for troubleshooting.
