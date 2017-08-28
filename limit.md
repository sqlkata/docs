# Limit and Offset

`Limit` and `Offset` allows you to limit the number of results returned from the database, this method is highly correlated with the `OrderBy` and `OrderByDesc` methods.

```cs
var latestPosts = new Query("Posts").OrderByDesc("Date").Limit(10)
```

In Sql Server
```sql
SELECT TOP (10) * FROM [Posts] ORDER BY [Date] DESC
```

In PostgreSql
```sql
SELECT * FROM "Posts" ORDER BY "Date" DESC LIMIT 10
```

In MySql

```sql
SELECT * FROM `Posts` ORDER BY `Date` DESC LIMIT 10
```

## Skipping records (Offset)

if you want to skip some records, use the `Offset` method.

```cs
var latestPosts = new Query("Posts").OrderByDesc("Date").Limit(10).Offset(5);
```

In Sql Server
```sql
SELECT * FROM (SELECT *, ROW_NUMBER() OVER (ORDER BY [Date] DESC) AS [row_num] FROM [Posts]) WHERE [row_num] BETWEEN 6 AND 15
```

In PostgreSql
```sql
SELECT * FROM "Posts" ORDER BY "Date" DESC LIMIT 10 OFFSET 5
```

In MySql
```sql
SELECT * FROM `Posts` ORDER BY `Date` DESC LIMIT 10 OFFSET 5
```

## Data pagination 

You can use the `ForPage` method to easily paginate your data.

```cs
var posts = new Query("Posts").OrderByDesc("Date").ForPage(2);
```

By default this method will return `15` rows per page, you can override this value by passing an integer as a 2nd parameter.

> **Note:** `ForPage` is 1-based so pass 1 for the first page


```cs
var posts = new Query("Posts").OrderByDesc("Date").ForPage(3, 50);
```


## Skip & Take
If you are coming from a `Linq` background here is a bonus for you. You can use the `Skip` and `Take` methods as aliases for `Offset` and `Limit` enjoy :)