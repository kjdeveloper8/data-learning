# Table joins
table: movies
```
 id |        title        |    director    | year | length_mintues 
----+---------------------+----------------+------+----------------
  1 | Toy Story           | John Lasseter  | 1995 |             81
  2 | A Bug's Life        | John Lasseter  | 1998 |             95
  3 | Toy Story 2         | John Lasseter  | 1999 |             93
  4 | Monsters, Inc.      | Pete Docter    | 2001 |             92
  5 | Finding Nemo        | Andrew Stanton | 2003 |            107
  6 | The Incredibles     | Brad Bird      | 2004 |            116
  7 | Cars                | John Lasseter  | 2006 |            117
  8 | Ratatouille         | Brad Bird      | 2007 |            115
  9 | WALL-E              | Andrew Stanton | 2008 |            104
 10 | Up                  | Pete Docter    | 2009 |            101
 11 | Toy Story 3         | Lee Unkrich    | 2010 |            103
 12 | Cars 2              | John Lasseter  | 2011 |            120
 13 | Brave               | Brenda Chapman | 2012 |            102
 14 | Monsters University | Dan Scanlon    | 2013 |            110
```

table: boxoffice
```
 movie_id | rating | domestic_sales | international_sales 
----------+--------+----------------+---------------------
        5 |    8.2 |  3.8084326e+08 |       5.5590003e+08
       14 |    7.4 |  2.6849277e+08 |       4.7506685e+08
        8 |      8 |  2.0644565e+08 |       4.1727715e+08
       12 |    6.4 |   1.914524e+08 |           3.684e+08
        3 |    7.9 |  2.4585218e+08 |       2.3916301e+08
        6 |      8 |  2.6144109e+08 |         3.70001e+08
        9 |    8.5 |  2.2380816e+08 |       2.9750368e+08
       11 |    8.4 |  4.1500486e+08 |       6.4816704e+08
        1 |    8.3 |  1.9179624e+08 |        1.701625e+08
        7 |    7.2 |  2.4408298e+08 |       2.1790016e+08
       10 |    8.3 |  2.9300416e+08 |        4.383386e+08
        4 |    8.1 |  2.8991626e+08 |           2.729e+08
        2 |    7.2 |  1.6279856e+08 |           2.006e+08
       13 |    7.2 |   2.372832e+08 |           3.017e+08
```

### Inner join

```sql
    select * from movies m join boxoffice b 
    on m.id = b.movie_id
    order by m.id;
```
```
 id |        title        |    director    | year | length_mintues | movie_id | rating | domestic_sales | international_sales 
----+---------------------+----------------+------+----------------+----------+--------+----------------+---------------------
-----------------
  1 | Toy Story           | John Lasseter  | 1995 |             81 |        1 |    8.3 |  1.9179624e+08 |        1.701625e+08
  2 | A Bug's Life        | John Lasseter  | 1998 |             95 |        2 |    7.2 |  1.6279856e+08 |           2.006e+08
  3 | Toy Story 2         | John Lasseter  | 1999 |             93 |        3 |    7.9 |  2.4585218e+08 |       2.3916301e+08
  4 | Monsters, Inc.      | Pete Docter    | 2001 |             92 |        4 |    8.1 |  2.8991626e+08 |           2.729e+08
  5 | Finding Nemo        | Andrew Stanton | 2003 |            107 |        5 |    8.2 |  3.8084326e+08 |       5.5590003e+08
  6 | The Incredibles     | Brad Bird      | 2004 |            116 |        6 |      8 |  2.6144109e+08 |         3.70001e+08
  7 | Cars                | John Lasseter  | 2006 |            117 |        7 |    7.2 |  2.4408298e+08 |       2.1790016e+08
  8 | Ratatouille         | Brad Bird      | 2007 |            115 |        8 |      8 |  2.0644565e+08 |       4.1727715e+08
  9 | WALL-E              | Andrew Stanton | 2008 |            104 |        9 |    8.5 |  2.2380816e+08 |       2.9750368e+08
 10 | Up                  | Pete Docter    | 2009 |            101 |       10 |    8.3 |  2.9300416e+08 |        4.383386e+08
 11 | Toy Story 3         | Lee Unkrich    | 2010 |            103 |       11 |    8.4 |  4.1500486e+08 |       6.4816704e+08
 12 | Cars 2              | John Lasseter  | 2011 |            120 |       12 |    6.4 |   1.914524e+08 |           3.684e+08
 13 | Brave               | Brenda Chapman | 2012 |            102 |       13 |    7.2 |   2.372832e+08 |           3.017e+08
 14 | Monsters University | Dan Scanlon    | 2013 |            110 |       14 |    7.4 |  2.6849277e+08 |       4.7506685e+08
```

##### Left join
```sql
    select * from cities c left join capitals p on c.name = p.name;

    -- using(col_name) :: in case of same col name in both tables
    select * from cities c left join capitals p using(name);
```
```
   name    | population | elevation |   name    | population | elevation | state 
-----------+------------+-----------+-----------+------------+-----------+-------
 Ahemdabad |      90200 |       410 | Ahemdabad |      90200 |       410 | GJ
 Dehli     |      50000 |       345 | Dehli     |      50000 |       345 | UP
 Jaypur    |      10000 |      2145 | Jaypur    |      10000 |      2145 | RJ
 Kerala    |      30100 |       654 | Kerala    |      30100 |       654 | TN
 Mumbai    |      40000 |       230 |           |            |           | 
 Pune      |      21000 |       625 | Pune      |      21000 |       625 | MH
 goa       |       5000 |       160 | goa       |       5000 |       160 | MA
 itanagar  |      35000 |       720 |           |            |           | 
```

##### Right join
```sql
    select * from cities c right join capitals p 
    on c.name = p.name;
```
```
   name    | population | elevation |   name    | population | elevation | state 
-----------+------------+-----------+-----------+------------+-----------+-------
 Ahemdabad |      90200 |       410 | Ahemdabad |      90200 |       410 | GJ
 Dehli     |      50000 |       345 | Dehli     |      50000 |       345 | UP
 Jaypur    |      10000 |      2145 | Jaypur    |      10000 |      2145 | RJ
 Kerala    |      30100 |       654 | Kerala    |      30100 |       654 | TN
 Pune      |      21000 |       625 | Pune      |      21000 |       625 | MH
 goa       |       5000 |       160 | goa       |       5000 |       160 | MA
```

##### Self join
```sql
   select distinct
    emp1.first_name || ' ' || emp1.last_name employee,
    emp2.first_name || ' ' || emp2.last_name employee,
	emp1.department_id department
    from employees emp1 inner join employees emp2
    on emp1.employee_id <> emp2.employee_id
    and emp1.department_id = emp2.department_id
    limit 8;
```
```
     employee      |     employee      | department 
-------------------+-------------------+------------
 David Austin      | Diana Lorentz     |         60
 Valli Pataballa   | Bruce Ernst       |         60
 Daniel Faviet     | John Chen         |        100
 Jose Manuel Urman | John Chen         |        100
 Luis Popp         | Jose Manuel Urman |        100
 Ismael Sciarra    | Luis Popp         |        100
 Luis Popp         | Nancy Greenberg   |        100
 Alexander Hunold  | Valli Pataballa   |         60
```

##### Cross join
```sql
    select c.name, p.state from cities c cross join capitals p limit 5;
```
```
   name    | state 
-----------+-------
 Mumbai    | MA
 Mumbai    | GJ
 Mumbai    | RJ
 Mumbai    | TN
 Mumbai    | UP
```