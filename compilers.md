# Compilers

The main goal of compilers is to transform the **Query** instance to a SQL string that can be executed directly by the database engine.


## Supported compilers
Currently SqlKata query builder supports natively the following compilers **SqlServer**, **MySql** and **PostgreSql**.

However writing a custom compiler should be so easy.

## Some noticeable difference
Theoretically the output of different compilers should be similar, this is true for the 80% of the cases, however in some edge cases the output can be very different, for instance take a look how the `Limit` and `Offset` clause get compiled in each compiler

```cs
new Query("Posts").Limit("10").Offset("20");
```

### SqlServer

```sql
SELECT * FROM (SELECT *, ROW_NUMBER() OVER (ORDER BY (SELECT 0)) AS [row_num] FROM [Posts]) WHERE [row_num] BETWEEN 21 AND 30
```

### MySql

```sql
SELECT * FROM `Posts` LIMIT 10 OFFSET 20
```

### PostgreSql

```sql
SELECT * FROM "Posts" LIMIT 10 OFFSET 20
```

