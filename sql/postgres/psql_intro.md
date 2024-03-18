# Introduction

#### Postgres (v.15)
Postgres is object-relational database management system (ORDBMS) that supports
- complex queries
- foreign keys
- triggers
- updatable views
- transactional integrity
- multiversion concurrency control

#### Installation
```shell
# mac
brew install postgresql@15 
# linux
sudo apt-get -y install postgresql
```
###### services
```shell
brew services start postgresql
brew services stop postgresql
brew services restart postgresql
```



##### general commands
- `/d  <table_name>` : describe table
- `\dt`: list of tables
- `\du`: list of roles
- `\copy` : copy from file or table in psql database and table
- `\! pwd` : present working detail
- `\! ls` : list directory
- `\timing` : turn on/off execution time
- `\c` : to change database
  

- import from csv
  ```sql
  \copy boxoffice from '/Users/krinaljoshi/Desktop/boxoffice.csv' with delimiter ',' csv header;
  ```  

- export to csv
  ```sql
  \copy (SELECT * FROM employees) to '/Users/krinaljoshi/Desktop/emp.csv' with csv
  ```

## Resources
👉 [Postgres docs](https://www.postgresql.org/docs/) \
👉 [Postgres tutorial](https://www.postgresqltutorial.com/)