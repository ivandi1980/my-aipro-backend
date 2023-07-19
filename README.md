# AI Project Backend

`Attention! This project is still in development.`

## Description
`AI Project Backend` is a backend for the AI Project. It is a REST API that allows the user to interact with the AI Project database.

## Installation
1. Clone the repository
2. Install the dependencies
3. Run the server
4. Enjoy!

## Usage

### Run Postgres Image
You can run the Postgres image with the following command:
```makefile
make postgresinit
```  

The setup for `postgresinit` is in the `Makefile`:
```makefile
postgresinit:
    docker run --name postgres15 -p 5433:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -d postgres:15-alpine
```  

### Create Database on Container  
If you want to create a database on the container, you can run the following command:
```bash
exec -it postgres15 createdb --username=postgres --owner=postgres ai-project
```  
to confirm that you're already in the container, you can run the following command:
```bash
exec -it postgres15 psql --username=postgres ai-project
```  
it would be showing the following output:
```bash
psql (15.0 (Debian 15.0-1.pgdg100+1))
Type "help" for help.

ai-project=# 
```  

### Steps to Create New backend  
Here are the steps to create a new backend:
```cgo
go mod init github.com/ai-project
```  
For handling connection to postgres database, you need to using somenthing like this:  
`_ "github.com/lib/pq"` which is imported in `db.go` file. (in my personal case only, maybe it would be different in your case)  

if you face any error, the easy way is to running the following command:
```cgo
go mod tidy
```  
and the error will be gone.

#### Create Table
To create a table, there are many ways to do it.
in this case, I'm using `golang-migrate` to create a table, because for me it most efficient way to create a table.  

for the installation, you can refer to this link: [golang-migrate](https://github.com/golang-migrate/migrate/tree/master/cmd/migrate)  
in my case, i using `MacOS` so i'm using `brew` to install it:
```bash
    brew install golang-migrate
```  

#### Create Migration
To create a migration, you can run the following command:
```bash
migrate create -ext sql -dir db/migrations -seq create_users_table
```  
it will create a file in `db/migrations` folder with the name `000001_create_users_table.up.sql`
then you can add sql query to create a table in that file.  
```sql
CREATE TABLE "users" (
    "id" bigserial primary key NOT NULL,
    "username" varchar NOT NULL,
    "email" varchar NOT NULL,
    "password" varchar NOT NULL
);
```  
there is another migration file with the name `000001_create_users_table.down.sql`  
it is used to drop the table.  
```sql
DROP TABLE IF EXISTS users;
```  

#### Run Migration