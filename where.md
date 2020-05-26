# Where
SqlKata offers many useful methods to make it easy writing `Where` conditions.

All these methods comes with overloads for the `NOT` and `OR` operators.

So you can use `OrWhereNull` to apply a boolean `OR` operator and `WhereNotNull` or `OrWhereNotNull` to negate the condition.

## Basic Where

The second parameter of the where method is optional and defaulting to `=` if omitted, so these two statements are totally the same.

```cs
//:playground
new Query("Posts").Where("Id", 10);

// since `=` is the default operator
new Query("Posts").Where("Id", "=", 10);
```

```cs
//:playground
new Query("Posts").WhereFalse("IsPublished").Where("Score", ">", 10);
```

```sql
SELECT * FROM [Posts] WHERE [IsPublished] = 0 AND [Score] > 10
```

> **Note:** The same apply for `WhereNot`, `OrWhere` and `OrWhereNot`.


## Multiple fields
If you want to filter your query against multiple fields, pass an `object` that represents col/values.

```cs
//:playground
var query = new Query("Posts").Where(new {
    Year = 2017 ,
    CategoryId = 198 ,
    IsPublished = true,
});
```

```sql
SELECT * FROM [Posts] WHERE [Year] = 2017 AND [CategoryId] = 198 AND [IsPublished] = True
```

## WhereNull, WhereTrue and WhereFalse
To filter against `NULL`, boolean `true` and boolean `false` values.

```cs
//:playground
db.Query("Users").WhereFalse("IsActive").OrWhereNull("LastActivityDate");
```

```sql
SELECT * FROM [Users] WHERE [IsActive] = cast(0 as bit) OR [LastActivityDate] IS NULL
```

> **Note:** the above methods will put the values literally in the generated sql and do not use parameter bindings techniques.

## Sub Query

You can pass a `Query` instance to compare a column against a sub query.

```cs
//:playground
var averageQuery = new Query("Posts").AsAverage("score");

var query = new Query("Posts").Where("Score", ">", averageQuery);
```

```sql
SELECT * FROM [Posts] WHERE [Score] > (SELECT AVG([score]) AS [avg] FROM [Posts])
```

> **Note:** The sub query should return one scalar cell to compare with, so you may need to set `Limit(1)` and select one column if needed


## Nested conditions and Grouping
To group your conditions, just wrap them inside another `Where` block.

```cs
//:playground
new Query("Posts").Where(q =>
    q.WhereFalse("IsPublished").OrWhere("CommentsCount", 0)
);
```

```sql
SELECT * FROM [Posts] WHERE ([IsPublished] = 0 OR [CommentsCount] = 0)
```

## Comparing two columns
Use this method when you want to compare two columns together.

```cs
//:playground
new Query("Posts").WhereColumns("Upvotes", ">", "Downvotes");
```

```sql
SELECT * FROM [Posts] WHERE [Upvotes] > [Downvotes]
```

## Where In
Pass an `IEnumerable<T>` to apply the SQL `WHERE IN` condition.
```cs
//:playground
new Query("Posts").WhereNotIn("AuthorId", new [] {1, 2, 3, 4, 5});
```

```sql
SELECT * FROM [Posts] WHERE [AuthorId] NOT IN (1, 2, 3, 4, 5)
```

You can pass a `Query` instance to filter against a sub query

```cs
//:playground
var blocked = new Query("Authors").Where("Status", "blocked").Select("Id");

new Query("Posts").WhereNotIn("AuthorId", blocked);
```

```sql
SELECT * FROM [Posts] WHERE [AuthorId] NOT IN (SELECT [Id] FROM [Authors] WHERE [Status] = 'blocked')
```

> **Note:** The sub query should return one column

## Where Exists

To select all posts that have at least one comment.

```cs
//:playground
new Query("Posts").WhereExists(q =>
    q.From("Comments").WhereColumns("Comments.PostId", "=", "Posts.Id")
);
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE EXISTS (SELECT TOP (1) 1 FROM [Comments] WHERE [Id] = [Posts].[Id])
```

In PostgreSql

```sql
SELECT * FROM "Posts" WHERE EXISTS (SELECT 1 FROM "Comments" WHERE "Id" = "Posts"."Id" LIMIT 1)
```

SqlKata tries to optimize the `EXISTS` query by disregarding the selected columns and limiting the result to `1` in order to provide a consistent behavior across all database engines.


## Where Raw
The `WhereRaw` methods allow you to write anything not supported by the methods above, so it will give you the maximum flexibility.


```cs
//:playground
new Query("Posts").WhereRaw("lower(Title) = ?", "sql");
```

```sql
SELECT * FROM [Posts] WHERE lower(Title) = 'sql'
```

Sometimes it's useful to wrap your table/columns by the engine identifier, this is helpful when the database is case sensitive like in PostgreSql, to do so just wrap your string with `[` and `]` and SqlKata will put the correspondent identifiers.

```cs
//:playground
new Query("Posts").WhereRaw("lower([Title]) = ?", "sql");
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE lower([Title]) = 'sql'
```

In PostgreSql
```sql
SELECT * FROM "Posts" WHERE lower("Title") = 'sql'
```

