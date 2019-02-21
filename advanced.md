# Advanced methods

## Conditional Statements

Sometimes you need to do some actions only when certain conditions are met, in these cases you can use the `When(condition, whenTrue, whenFalse = null)` method.

```cs
var query = db.Query("Transactions");

query.When(
    amount > 0,
    q => q.Select("Debit as Amount"),
    q => q.Select("Credit as Amount"),
)

```

is the same as

```cs
var query = db.Query("Transactions");

if(amount > 0)
{
    query.Select("Debit as Amount");
}
else
{
    query.Select("Credit as Amount");
}
```

Off course you can use to build any part of the query.

## Clone

`Query`s instances are mutable, this means that modifying the copied query will also change the original query.

To make a real clone of the query instance, you should use the `Clone` method.

```cs
var baseQuery = new Query().Select("Id", "Name").Limit(10).OrderBy("Date");

var posts = baseQuery.Clone().From("Posts");
var authors = baseQuery.Clone().From("Authors").Limit(100); // override the limit value
var sites = baseQuery.Clone().From("Sites");
```

## Engine specific queries

SqlKata allows you to tune your queries against specific engines by using the `ForXXX` methods.

This is helpful when you want to apply some native functions, that are available in some vendors and not in others.



### Casting Example

```cs
var query = new Query("Posts")
    .Select("Id", "Title")
    .ForPostgres(q => q.SelectRaw("[Date]::date"))
    .ForSqlServer(q => q.SelectRaw("CAST([Date] as DATE)"));
```

In Sql Server

```sql
SELECT [Id], [Title], CAST([Date] as DATE) FROM [Posts]
```

In PostgreSql

```sql
SELECT "Id", "Title", "Date"::date FROM "Posts"
```

In this example, Mysql isn't affected

```sql
SELECT `Id`, `Title` FROM `Posts`
```

### Generating date series example

Another example is to generate a date series between two given dates, you can use the `generate_series` in PostgreSql, and use a Recursion CTE in SqlServer.


```cs
var now = DateTime.UtcNow;
var format = "yyyy-MM-dd";

DateTime from = now.AddDays(-5).ToString(format),
         now.ToString(format);

var rangeQuery = new Query()

.ForPostgres(q =>

    // everything written here is available to the Postgre Compiler only
    q.FromRaw("generate_series ( ?::timestamp, ?::timestamp, '1 day'::interval) dates", new[] { from, to })
    .SelectRaw("dates::date as date")
)

.ForSqlServer(q =>

    // everything written here is available to the SqlServer Compiler only
    q.WithRaw("range", @"SELECT CAST(? AS DATETIME) 'date'
        UNION ALL
        SELECT DATEADD(dd, 1, t.date) FROM range t WHERE DATEADD(dd, 1, t.date) <= ?", new[] { from, to }
    )
    .From("range")
)
```
Although it's quite complicated, don't worry just focus on the concept for now.

The following will output:

In Sql Server

```sql
WITH [range] AS (
    SELECT CAST('2017-08-23' AS DATETIME) 'date'
    UNION ALL
    SELECT DATEADD(dd, 1, t.date)
    FROM range t
    WHERE DATEADD(dd, 1, t.date) <= '2017-08-28'
)
SELECT * FROM [range]
```

In PostgreSql

```sql
SELECT dates::date as date FROM generate_series ( '2017-08-23'::timestamp, '2017-08-28'::timestamp, '1 day'::interval) dates
```

Off course you can use any method you want inside these lambdas