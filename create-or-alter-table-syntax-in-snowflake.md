# CREATE OR ALTER TABLE syntax in Snowflake

Snowflake now support CREATE OR ALTER TABLE syntax, where existing tables can be altered in a single CREATE TABLE DDL statement

## Existing Table

| name                  | type        | kind   | null? | default | primary key |
|-----------------------|-------------|--------|-------|---------|-------------|
| HOTEL_ID              | VARCHAR(10) | COLUMN | Y     |         | N           |
| RATING                | NUMBER(1,0) | COLUMN | Y     |         | N           |
| PRICE                 | NUMBER(3,0) | COLUMN | Y     |         | N           |
| DISTANCE_FROM_CENTER  | NUMBER(1,0) | COLUMN | Y     |         | N           |

## CREATE OR ALTER TABLE statement

```sql
create or alter TABLE HOTELS (
	HOTEL_ID VARCHAR(10) PRIMARY KEY,
	RATING NUMBER(1,0),
	PRICE NUMBER(3,0),
	DISTANCE_FROM_CENTER NUMBER(1,0), 
    DISTANCE_FROM_AIRPORT NUMBER(1,0)
);
```

## Updated Table

| name                  | type        | kind   | null? | default | primary key |
|-----------------------|-------------|--------|-------|---------|-------------|
| HOTEL_ID              | VARCHAR(10) | COLUMN | Y     |         | Y           |
| RATING                | NUMBER(1,0) | COLUMN | Y     |         | N           |
| PRICE                 | NUMBER(3,0) | COLUMN | Y     |         | N           |
| DISTANCE_FROM_CENTER  | NUMBER(1,0) | COLUMN | Y     |         | N           |
| DISTANCE_FROM_AIRPORT | NUMBER(1,0) | COLUMN | Y     |         | N           |


