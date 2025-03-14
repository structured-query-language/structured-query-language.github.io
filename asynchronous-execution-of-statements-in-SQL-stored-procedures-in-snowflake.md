# Asynchronous execution of statements in SQL stored procedures in Snowflake

Snowflake supports `async`/`await` syntactic sugar that allows asynchronous, non-blocking stored procedures to be structured in a way similar to an ordinary synchronous stored procedures. `async`/`await` syntactic sugar increases readability of the code.

Here is a sample:

```sql

create or replace table customer_emails (customer_id int, email_address string);
create or replace table order_emails (order_number int, email_address string);

CREATE or replace PROCEDURE sp__async_insert (order_number int, customer_id int, email_address string)
RETURNS string
LANGUAGE SQL
  AS
    BEGIN
      ASYNC (INSERT INTO customer_emails VALUES (:customer_id, :email_address));
      ASYNC (INSERT INTO order_emails VALUES (:order_number, :email_address)) ;
      AWAIT ALL;
    END
;

call sp__async_insert(111, 123, 'name@domain.tld');
select * from customer_emails;
select * from order_emails;

```

![carbon(9)](https://github.com/user-attachments/assets/8b3543a6-1c83-41fa-a7e7-b4576b506c66)


