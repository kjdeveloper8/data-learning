# Window function
> A window function performs a calculation across a set of table rows that are somehow related to the current row. This is comparable to the type of calculation that can be done with an aggregate function. But unlike regular aggregate functions, use of a window function does not cause rows to become grouped into a single output row — the rows retain their separate identities. Behind the scenes, the window function is able to access more than just the current row of the query result.  [[source]](https://www.postgresql.org/docs/15/tutorial-window.html)

> Unlike aggregate functions, window functions return a single value for each row from the underlying query. The window is defined using the `OVER()` clause.

#### example
```sql
-- create table
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
```sql
-- insert values
Insert into employees 
(EMPLOYEE_ID,FIRST_NAME,LAST_NAME,EMAIL,PHONE_NUMBER,HIRE_DATE,JOB_ID,SALARY,DEPARTMENT_ID) values 
(1,'Curtis','Davies','curtis@psql.com','650-121-2994',to_date('29-01-05','DD-MM-YY'),'ST_CLERK',3100,50),
(2,'Randall','Matos','rmatos@psql.com','650-121-2874',to_date('15-03-06','DD-MM-YY'),'ST_CLERK',2600,50),
(3,'Alexander','Hunold','alex@gmail.com','590-423-4567',to_date('03-01-06','DD-MM-YY'),'IT_PROG',9000,60),
(4,'Bruce','Ernst','brunce@gmail.com','590-423-4568',to_date('21-05-07','DD-MM-YY'),'IT_PROG',6000,60),
(5,'David','Austin','dav@outlook.com','590-423-4569',to_date('25-06-05','DD-MM-YY'),'IT_PROG',4800,60),
(6,'Valli','Pataballa','vp@gmail.com','590-423-4560',to_date('05-02-06','DD-MM-YY'),'IT_PROG',4800,60),
(7,'Diana','Lorentz','dia@psql.com','590-423-5567',to_date('07-02-07','DD-MM-YY'),'IT_PROG',4200,60),
(8,'Nancy','Greenberg','nberg@outlook.com','515-124-4569',to_date('17-08-02','DD-MM-YY'),'FI_MGR',12008,100),
(9,'Daniel','Faviet','dfavi@gmail.com','515-124-4169',to_date('16-08-02','DD-MM-YY'),'FI_ACCOUNT',9000,100),
(10,'John','Chen','chen@abc.com','515-124-4269',to_date('28-09-05','DD-MM-YY'),'FI_ACCOUNT',8200,100),
(11,'Ismael','Sciarra','isis@abc.com','515-124-4369',to_date('30-09-05','DD-MM-YY'),'FI_ACCOUNT',7700,100),
(12,'Jose Manuel','Urman','jo@gmail.com','515-124-4469',to_date('07-03-06','DD-MM-YY'),'FI_ACCOUNT',7800,100),
(13,'Luis','Popp','lucy@outlook.com','515-124-4567',to_date('07-12-07','DD-MM-YY'),'FI_ACCOUNT',6900,100),
(14,'Den','Raphaely','den@gmail.com','515-127-4561',to_date('07-12-02','DD-MM-YY'),'PU_MAN',11000,30);
```
#### how to compare each employee's salary with the average salary in his or her department 

```sql
SELECT department_id, employee_id, first_name, salary, avg(salary) OVER (PARTITION BY department_id) FROM employees;
```
```
 department_id | employee_id | first_name  |  salary  |          avg           
---------------+-------------+-------------+----------+------------------------
            30 |          14 | Den         | 11000.00 | 11000.0000000000000000
            50 |           2 | Randall     |  2600.00 |  2850.0000000000000000
            50 |           1 | Curtis      |  3100.00 |  2850.0000000000000000
            60 |           4 | Bruce       |  6000.00 |  5760.0000000000000000
            60 |           5 | David       |  4800.00 |  5760.0000000000000000
            60 |           6 | Valli       |  4800.00 |  5760.0000000000000000
            60 |           7 | Diana       |  4200.00 |  5760.0000000000000000
            60 |           3 | Alexander   |  9000.00 |  5760.0000000000000000
           100 |           9 | Daniel      |  9000.00 |  8601.3333333333333333
           100 |          10 | John        |  8200.00 |  8601.3333333333333333
           100 |          11 | Ismael      |  7700.00 |  8601.3333333333333333
           100 |          12 | Jose Manuel |  7800.00 |  8601.3333333333333333
           100 |          13 | Luis        |  6900.00 |  8601.3333333333333333
           100 |           8 | Nancy       | 12008.00 |  8601.3333333333333333
```

#### partititon by job_id, Order by salary
```sql
SELECT job_id, employee_id, first_name, salary,
       rank() OVER (PARTITION BY job_id ORDER BY salary DESC)
FROM employees;
```
```
   job_id   | employee_id | first_name  |  salary  | rank 
------------+-------------+-------------+----------+------
 FI_ACCOUNT |           9 | Daniel      |  9000.00 |    1
 FI_ACCOUNT |          10 | John        |  8200.00 |    2
 FI_ACCOUNT |          12 | Jose Manuel |  7800.00 |    3
 FI_ACCOUNT |          11 | Ismael      |  7700.00 |    4
 FI_ACCOUNT |          13 | Luis        |  6900.00 |    5
 FI_MGR     |           8 | Nancy       | 12008.00 |    1
 IT_PROG    |           3 | Alexander   |  9000.00 |    1
 IT_PROG    |           4 | Bruce       |  6000.00 |    2
 IT_PROG    |           5 | David       |  4800.00 |    3
 IT_PROG    |           6 | Valli       |  4800.00 |    3
 IT_PROG    |           7 | Diana       |  4200.00 |    5
 PU_MAN     |          14 | Den         | 11000.00 |    1
 ST_CLERK   |           1 | Curtis      |  3100.00 |    1
 ST_CLERK   |           2 | Randall     |  2600.00 |    2
```

#### sum (salary)
```sql
SELECT salary, sum(salary) OVER (ORDER BY salary) FROM employees;
```
```
  salary  |   sum    
----------+----------
  2600.00 |  2600.00
  3100.00 |  5700.00
  4200.00 |  9900.00
  4800.00 | 19500.00
  4800.00 | 19500.00
  6000.00 | 25500.00
  6900.00 | 32400.00
  7700.00 | 40100.00
  7800.00 | 47900.00
  8200.00 | 56100.00
  9000.00 | 74100.00
  9000.00 | 74100.00
 11000.00 | 85100.00
 12008.00 | 97108.00
```

#### window calculation with sub selection
```sql
SELECT job_id, employee_id, first_name, salary, hire_date
FROM
  (SELECT job_id, employee_id, first_name, salary, hire_date,
          rank() OVER (PARTITION BY job_id ORDER BY salary DESC, employee_id) AS pos
     FROM employees
  ) AS ss
WHERE pos < 3;
```
```
   job_id   | employee_id | first_name |  salary  | hire_date  
------------+-------------+------------+----------+------------
 FI_ACCOUNT |           9 | Daniel     |  9000.00 | 2002-08-16
 FI_ACCOUNT |          10 | John       |  8200.00 | 2005-09-28
 FI_MGR     |           8 | Nancy      | 12008.00 | 2002-08-17
 IT_PROG    |           3 | Alexander  |  9000.00 | 2006-01-03
 IT_PROG    |           4 | Bruce      |  6000.00 | 2007-05-21
 PU_MAN     |          14 | Den        | 11000.00 | 2002-12-07
 ST_CLERK   |           1 | Curtis     |  3100.00 | 2005-01-29
 ST_CLERK   |           2 | Randall    |  2600.00 | 2006-03-15
```

#### window with each result operation
```sql
SELECT sum(salary) OVER w, avg(salary) OVER w
  FROM employees
  WINDOW w AS (PARTITION BY job_id ORDER BY salary DESC);
```
```
   sum    |          avg           
----------+------------------------
  9000.00 |  9000.0000000000000000
 17200.00 |  8600.0000000000000000
 25000.00 |  8333.3333333333333333
 32700.00 |  8175.0000000000000000
 39600.00 |  7920.0000000000000000
 12008.00 | 12008.0000000000000000
  9000.00 |  9000.0000000000000000
 15000.00 |  7500.0000000000000000
 24600.00 |  6150.0000000000000000
 24600.00 |  6150.0000000000000000
 28800.00 |  5760.0000000000000000
 11000.00 | 11000.0000000000000000
  3100.00 |  3100.0000000000000000
  5700.00 |  2850.0000000000000000
  ```

#### sum, count, avg
```sql
SELECT job_id,
       salary,
       SUM(salary) OVER
         (PARTITION BY job_id) AS sum_salary,
       COUNT(salary) OVER
         (PARTITION BY job_id) AS salary_count,
       AVG(salary) OVER
         (PARTITION BY job_id) AS salary_avg
  FROM employees;
```
```
   job_id   |  salary  | sum_salary | salary_count |       salary_avg       
------------+----------+------------+--------------+------------------------
 FI_ACCOUNT |  7800.00 |   39600.00 |            5 |  7920.0000000000000000
 FI_ACCOUNT |  6900.00 |   39600.00 |            5 |  7920.0000000000000000
 FI_ACCOUNT |  9000.00 |   39600.00 |            5 |  7920.0000000000000000
 FI_ACCOUNT |  8200.00 |   39600.00 |            5 |  7920.0000000000000000
 FI_ACCOUNT |  7700.00 |   39600.00 |            5 |  7920.0000000000000000
 FI_MGR     | 12008.00 |   12008.00 |            1 | 12008.0000000000000000
 IT_PROG    |  4800.00 |   28800.00 |            5 |  5760.0000000000000000
 IT_PROG    |  4200.00 |   28800.00 |            5 |  5760.0000000000000000
 IT_PROG    |  9000.00 |   28800.00 |            5 |  5760.0000000000000000
 IT_PROG    |  6000.00 |   28800.00 |            5 |  5760.0000000000000000
 IT_PROG    |  4800.00 |   28800.00 |            5 |  5760.0000000000000000
 PU_MAN     | 11000.00 |   11000.00 |            1 | 11000.0000000000000000
 ST_CLERK   |  2600.00 |    5700.00 |            2 |  2850.0000000000000000
 ST_CLERK   |  3100.00 |    5700.00 |            2 |  2850.0000000000000000
```

#### row_number : counts number of row
```sql
SELECT job_id,
       first_name,
       salary,
       ROW_NUMBER() OVER (ORDER BY first_name)
                    AS row_number
  FROM employees;
```
```
   job_id   | first_name  |  salary  | row_number 
------------+-------------+----------+------------
 IT_PROG    | Alexander   |  9000.00 |          1
 IT_PROG    | Bruce       |  6000.00 |          2
 ST_CLERK   | Curtis      |  3100.00 |          3
 FI_ACCOUNT | Daniel      |  9000.00 |          4
 IT_PROG    | David       |  4800.00 |          5
 PU_MAN     | Den         | 11000.00 |          6
 IT_PROG    | Diana       |  4200.00 |          7
 FI_ACCOUNT | Ismael      |  7700.00 |          8
 FI_ACCOUNT | John        |  8200.00 |          9
 FI_ACCOUNT | Jose Manuel |  7800.00 |         10
 FI_ACCOUNT | Luis        |  6900.00 |         11
 FI_MGR     | Nancy       | 12008.00 |         12
 ST_CLERK   | Randall     |  2600.00 |         13
 IT_PROG    | Valli       |  4800.00 |         14
```

#### ntile: to identify what percentile a given row falls into

```sql
SELECT job_id,
       salary,
       NTILE(4) OVER
         (PARTITION BY job_id ORDER BY salary)
          AS quartile,
       NTILE(5) OVER
         (PARTITION BY job_id ORDER BY salary)
         AS quintile,
       NTILE(100) OVER
         (PARTITION BY job_id ORDER BY salary)
         AS percentile
  FROM employees
 ORDER BY job_id, salary;
```
##### window alias
```sql
SELECT job_id,
       salary,
       NTILE(4) OVER ntile_window AS quartile,
       NTILE(5) OVER ntile_window AS quintile,
       NTILE(100) OVER ntile_window AS percentile
  FROM employees
WINDOW ntile_window AS
         (PARTITION BY job_id ORDER BY salary)
 ORDER BY job_id, salary;
```
```
   job_id   |  salary  | quartile | quintile | percentile 
------------+----------+----------+----------+------------
 FI_ACCOUNT |  6900.00 |        1 |        1 |          1
 FI_ACCOUNT |  7700.00 |        1 |        2 |          2
 FI_ACCOUNT |  7800.00 |        2 |        3 |          3
 FI_ACCOUNT |  8200.00 |        3 |        4 |          4
 FI_ACCOUNT |  9000.00 |        4 |        5 |          5
 FI_MGR     | 12008.00 |        1 |        1 |          1
 IT_PROG    |  4200.00 |        1 |        1 |          1
 IT_PROG    |  4800.00 |        1 |        2 |          2
 IT_PROG    |  4800.00 |        2 |        3 |          3
 IT_PROG    |  6000.00 |        3 |        4 |          4
 IT_PROG    |  9000.00 |        4 |        5 |          5
 PU_MAN     | 11000.00 |        1 |        1 |          1
 ST_CLERK   |  2600.00 |        1 |        1 |          1
 ST_CLERK   |  3100.00 |        2 |        2 |          2
```

#### LAG or LEAD: to create columns that pull values from other rows
LAG pulls from previous rows 
LEAD pulls from next rows

```sql
SELECT job_id, first_name,
       department_id,
       LAG(department_id, 1) OVER
         (PARTITION BY job_id ORDER BY department_id) AS lag,
       LEAD(department_id, 1) OVER
         (PARTITION BY job_id ORDER BY department_id) AS lead
  FROM employees
 WHERE hire_date < '2006-01-05'
 ORDER BY job_id, first_name, department_id;
```
```
   job_id   | first_name | department_id | lag | lead 
------------+------------+---------------+-----+------
 FI_ACCOUNT | Daniel     |           100 | 100 |  100
 FI_ACCOUNT | Ismael     |           100 |     |  100
 FI_ACCOUNT | John       |           100 | 100 |     
 FI_MGR     | Nancy      |           100 |     |     
 IT_PROG    | Alexander  |            60 |     |   60
 IT_PROG    | David      |            60 |  60 |     
 PU_MAN     | Den        |            30 |     |     
 ST_CLERK   | Curtis     |            50 |     |     
```









