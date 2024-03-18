# Constraints

### Primary key constraint
```sql
    create table orders(
    order_id serial primary key, -- pk
    customer_id varchar(255) not null, 
    order_date date not null
    );
```
```sql
    create table order_items(
    order_id int, 
    item_no serial, 
    item_description varchar not null, 
    quantity integer not null, 
    price dec(10, 2), 
    primary key (order_id, item_no) -- pk
    );
```

### Foreign key constraint
```sql
    create table customers(
    customer_id int generated always as identity,
    customer_name varchar(255) not null,
    primary key(customer_id) -- pk
    );

    create table contacts(
    contact_id int generated always as identity,
    customer_id int,
    contact_name varchar(255) not null,
    phone varchar(15),
    email varchar(100),
    primary key(contact_id),
    constraint fk_customer foreign key(customer_id) -- fk
    references customers(customer_id)
    on delete cascade
    );
```
### Check constraint
```sql
    create table employees (
    id serial primary key, 
    first_name varchar (50) not null, 
    last_name varchar (50) not null,  
    birth_date date not null, 
    joined_date date not null, 
    salary numeric check(salary > 0) -- check
    );
```

### Unique constraint
```sql
    create table person (
    id serial primary key, 
    first_name varchar (50), 
    last_name varchar (50), 
    email varchar (50), 
    unique(email) -- unique
    );
```
### Not null
```sql
    create table invoices(
    id serial primary key,
    product_id int not null, -- not null
    qty numeric not null check(qty > 0), -- not null
    net_price numeric check(net_price > 0) 
    );
```