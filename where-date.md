Date Operations
=================

SqlKata Query Builder provides `WhereDate`, `WhereTime`, and `WhereDatePart` methods to deal with date columns.

this is useful if you want to query against a date part of the column.

##WhereDate
let you query against the **date part** of a datetime column

```cs
new Query("Posts").WhereDate("CreatedAt", "2018-04-01");
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE CAST([CreatedAt] as date) = '2018-04-01'
```

In PostgreSql

```sql
SELECT * FROM "Posts" WHERE "CreatedAt"::date = '2018-04-01'
```

In MySql

```sql
SELECT * FROM `Posts` WHERE DATE(`CreatedAt`) = '2018-04-01'
```

##WhereTime
let you query against the **time part** of a datetime column

```cs
new Query("Posts").WhereTime("CreatedAt", ">", "16:30");
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE CAST([CreatedAt] as time) > '16:30'
```

In PostgreSql
```sql
SELECT * FROM "Posts" WHERE "CreatedAt"::time > '16:30'
```

In MySql
```sql
SELECT * FROM `Posts` WHERE TIME(`CreatedAt`) > '16:30'
```

##WhereDatePart
**WhereDatePart** accept a `datePart` argument to specify the part you want to query against, the available options are: **date**, **time**, **year**, **month**, **day**, **hour** and **minute**.

For example to get the posts created in the first of February.

```cs
new Query("Posts").WhereDatePart("day", "CreatedAt", 1).WhereDatePart("month", "CreatedAt", 2);
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE DATEPART('DAY', [CreatedAt]) = 1 AND DATEPART('MONTH', [CreatedAt]) = 2
```

In Postgres
```sql
SELECT * FROM "Posts" WHERE DATE_PART('DAY', "CreatedAt") = 1 AND DATE_PART('MONTH', "CreatedAt") = 2
```

In MySql
```sql
SELECT * FROM `Posts` WHERE DAY(`CreatedAt`) = 1 AND MONTH(`CreatedAt`) = 2
```