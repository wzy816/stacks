# cassandra

# cql

## query

```sql
-- select
select * from my_table;

-- count
select count(*) from my_table;
```

## create table

```sql
-- one primary key
CREATE table tsp_moter_speed_torque (
      sample_ts timestamp,
      _id varchar PRIMARY KEY,
);

-- multiple primary key
CREATE table tsp_moter_speed_torque (
      sample_ts timestamp,
      _id varchar,
      PRIMARY KEY(sample_ts,_id)
);
```
