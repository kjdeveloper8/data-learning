# Transaction

##### Create sample table
```sql
    -- create table
    CREATE TABLE accounts (
        id INT GENERATED BY DEFAULT AS IDENTITY,
        name VARCHAR(100) NOT NULL,
        balance DEC(15,2) NOT NULL CHECK(balance >= 0),
        PRIMARY KEY(id)
    );

    -- insert data
    insert into accounts(name, balance)
    values('Alex',10000), ('Lucy', 50000), ('Zeno',80000);
```

##### transaction proceess
```sql
    -- insert new entry
    begin;
    insert into accounts(name, balance) values ('Kenna',25000);
    commit; -- finalize changes

    -- update
    begin;
    update accounts set balance = balance - 100
    where id = 2; 
    -- bal : 49,900
    rollback; -- undo the changes
    -- bal : 50,000
    update accounts set balance = balance - 500
    where id  = ;
    -- bal : 49,500
    commit; -- finalize changes
    -- bal : 49,500

```


