# Postgres Queries

### Create
create table
```sql
    create table movies (
    id int,
    title varchar(50),
    director varchar(50),
    year int,
    length_mintues int);
```
create view
```sql
    create view myview as
    select name, temp_min, temp_max, date, location
    from weather, cities
    where city = name;

    -- use it as table
    select * from myview;
```
domain creation
```sql
    -- domain for email validation
    CREATE DOMAIN valid_email AS text
    CHECK(
    VALUE ~ '^\w+@[a-zA-Z_]+?\.[a-zA-Z]{2,3}$' 
    );

    -- usage
    CREATE TABLE employees
    (
        employee_id integer NOT NULL,
        first_name varchar(20) NOT NULL,
        last_name varchar(25) NOT NULL,
        email valid_email NOT NULL, -- constraint domain
        phone_number varchar(20),
        hire_date date NOT NULL,
        job_id character varying(10) NOT NULL,
        salary numeric(8,2),
        department_id integer,
        CONSTRAINT emp_id_pk PRIMARY KEY (employee_id)
    );
```

### Insert
```sql
    insert into cities values ('Mumbai', 40000, 230), ('itanagar', 35000, 720);
    insert into capitals (name, population, state, elevation) values ('Mumbai', 40000, 'MH', 230);
```

### Alter
```sql
    -- alter existing table col
    alter table movies 
    alter column year type real,
    alter column title type varchar(30);

    -- rename col
    alter table movies
    rename column length_mintues to len_min;

    -- rename table
    alter table movies2
    rename to movies_2

    -- add new col
    alter table customer
    add column name varchar(20) not null,
    add column email varchar(30);

    -- add primary key constraint to name col
    alter table cities 
    add primary key (name); 

    -- add foreign key
    alter table weather 
    add constraint cityfk foreign key (city) references cities(name); 

    -- add check constraint
    alter table employees
    add constraint joined_date_check
    check ( joined_date >  birth_date );

    -- drop constraints
    alter table cities
    drop constraint citypk;

    -- drop col
    alter table cities
    drop column temp cascade; -- in case of depedency objects
```
### Update
```sql
    -- update value
    update weather set prcp = prcp + 0.05 
    where city = 'San Francisco' and date = '1994-11-27';
```

### Delete
```sql
    -- drop table 
    drop table cities;

    -- truncate (delete all data from table)
    truncate table weather;

    -- delete row
    delete from employees where id = 3;
```

### Select
```sql
    -- returns all data
    select * from movies;
    
    -- returns row that contains id 1
    select * from employees where employee_id=1;

    --returns row contains name starts with 'Al%' (case sensitive)
    select first_name, last_name from employees where first_name like 'Al%';

    --returns row contains name 
    select first_name, last_name from employees where first_name in ('David', 'Diana');

    --returns row contains name starts with 'Da' and last name not with 'Loren'
    select first_name, last_name from employees where first_name like 'Da%' and last_name <> 'Loren';

    -- returns row between 
    select first_name ||' '||last_name "full name", phone_number from employees where hire_date between '01-01-2005' and '01-01-2007' and job_id = 'IT_PROG';

    -- returns first and last name as one full_name (or use "full name" with "" for spaced alias)
    select first_name ||' '|| last_name as full_name, email from employees limit 4;

    -- returns length of first name with desc order
    select first_name, length(first_name) len from 
    employees order by len desc;

    -- evaluate duplicate values in director col
    select distinct director from movies; 

    -- fetch
    select * from movies fetch first row only;
    
    -- offset (no_of_row to skip) fetch after that :: skip 3 rows and than fetch first 5 after that 
    select * from movies offset 3 rows fetch first 5 row only;

    -- group by having
    select first_name, sum(salary) "salary" from employees 
    group by employee_id having sum(salary) > 6000 order by salary desc;

    -- count no of rows (returns 14)
    select count(salary) from employees ;
```
### create table from existing one
Select into
```sql
    -- create table from existing one
    select id, title, length_mintues into table movies2
    from movies where length_mintues < 100 order by title;

    -- created table
    select * from movie2;
```
create as query
```sql
    -- create table as query
    create table lass_movies
    as 
    select m.id, m.title, m.year, b.rating
    from movies m join boxoffice b 
    on m.id = b.movie_id
    where m.director = 'John Lasseter' -- use '' instead ""
    order by m.year;

    -- created table
    select * from lass_movies;
```


# Subquery
Example
```sql
    -- returns rows from capitals table same as city name in cities table
    select * from capitals where name = any (select name from cities);
    
    -- returns rows higher or lower that raing in boxoffice table
    select * from movies where id > all (select rating from boxoffice);
    select * from movies where id < all (select rating from boxoffice);
```

#### common table expression (cte)
Example 1
```sql
    -- return movie title like 'Story' order by rating
    with classic_movie as(
    select m.title, m.year, b.rating 
    from movies m join boxoffice b 
    on m.id = b.movie_id
    where m.title like '%Story%'
    order by b.rating desc
    )
    select * from classic_movie;
```

Example 2
```sql
    -- calculate weather stats
    with weather_stats as(
        select avg(prcp) as av_prcp,
        max(temp_max) as tmax,
        min(temp_min) as tmin
        from weather
    ),
    -- calculate city stats
    city_stats as(
        select name as dense_city from cities where population=(select max(population) from cities)
    ),
    -- calculate population stats
    population_stats as(
        select sum(population) as p from cities
    )
    select
    (select tmin from weather_stats) as "min temp",
    (select tmax from weather_stats) as "max temp",
    (select dense_city from city_stats) as "Dense city",
    (select p from population_stats) as "total population"; 
```
NOTE: subquery must return only one column

### create sequence

```sql
    -- create seq starting from 3 with 5 increment within range (1, 20)
    create sequence plus_five
    increment 5
    minvalue 1 
    maxvalue 20
    start 3
    cycle;

    -- result: 3, 8, 13, 18, 1, 6, 11, 16, 1, 6 ...
    select nextval('plus_five');

    -- drop seq
    drop sequence plus_five;
```

### Inheritance
```sql
    create table cities (
    name       text,
    population real,
    elevation  int     -- (in ft)
    );

    create table capitals (
    state      char(2) unique not null
    ) inherits (cities); -- inherits all cities col
```
NOTE : insert data into child table which also adds data to parent table BUT if you insert data into parent table only it does not added into child table

```sql
    insert into cities values ('Mumbai', 40000, 230), ('itanagar', 35000, 720);
    -- only adds into parent table
    insert into cities (name, population, state, elevation) values ('Mumbai', 40000, 230); -- generates error (not have access to child table col 'state')
```
```sql
    insert into capitals (name, population, state, elevation) values ('goa', 5000, 'MA', 160);
    -- this will inserts data into parent table as well

    insert into capitals (name, population, elevation, state) values ('goa', 5000, 160, 'MA'), ('Ahemdabad', 90200, 410, 'GJ'), ('Jaypur', 10000, 2145, 'RJ'), ('Kerala', 30100, 654, 'TN'), ('Dehli', 50000, 345, 'UP'), ('Pune', 21000, 625, 'MH');
```
```sql
    select name, elevation from cities where elevation > 500; 
    -- fetches data from both parent and child table

    select name, elevation from only cities where elevation > 500;
    -- fetches data from only parent table not from child
```







