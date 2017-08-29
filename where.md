# Where
SqlKata offers many useful methods to make it easy writing `Where` conditions.

All these methods comes with overloads for the `NOT` and `OR` operators.

So you can use `OrWhereNull` to apply a boolean `OR` operator and `WhereNotNull` or `OrWhereNotNull` to negate the condition.

## Basic Where

The second parameter of the where method is optional and defaulting to `=` if omitted, so these two statements are totally the same.

```cs
new Query().Where("Id", 10);

// since = is the default
new Query().Where("Id", "=", 10);
``` 

```cs
new Query("Posts").WhereNot("IsPublished", true).Where("Score", ">", 10);
```

```sql
SELECT * FROM [Posts] WHERE NOT ([IsPublished] = True) AND [Score] > 10
```

> **Note:** The same apply for `WhereNot`, `OrWhere` and `OrWhereNot`.


## Multiple fields
If you want to filter your query against multiple fields, pass a `Dictionary<string, object>`.

```cs
var constraints = new Dictionary<string, object> {
    { "Year", 2017 },
    { "CategoryId", 198 },
    { "IsPublished", true }
};

var query = new Query("Posts").Where(constraints);
```

```sql
SELECT * FROM [Posts] WHERE [Year] = 2017 AND [CategoryId] = 198 AND [IsPublished] = True
```

## Sub Query

You can pass a `Query` instance to compare a column against a sub query.

```cs
var average = new Query("Posts").Average("score");

new Query("Posts").Where("Score", ">", average);
```

```sql
SELECT * FROM [Posts] WHERE [Score] > (SELECT AVG([score]) AS [avg] FROM [Posts])
```

> **Note:** The sub query should return one scalar cell to compare with, so you may need to set `Limit(1)` and select one column if needed


## Nested conditions and Grouping
To group your conditions, just wrap them inside another `Where` block.

```cs
new Query("Posts").Where(q => 
    q.Where("IsPublished", false).OrWhere("CommentsCount", 0)
).WhereNot(q => 
    q.WhereNull("AuthorId").OrWhereNull("Title") 
);
```

```sql
SELECT * FROM [Posts] WHERE ([IsPublished] = False OR [CommentsCount] = 0) AND NOT ([AuthorId] IS NULL OR [Title] IS NULL)
```

## Comparing two columns
Use this method when you want to compare two columns together.

```cs
new Query("Posts").WhereColumns("Upvotes", ">", "Downvotes");
```

```sql
SELECT * FROM [Posts] WHERE [Upvotes] > [Downvotes]
```

## Where In
Pass an `IEnumerable<T>` to apply the SQL `WHERE IN` condition.
```cs
new Query("Posts").WhereNotIn("AuthorId", new [] {1, 2, 3, 4, 5});
```

```sql
SELECT * FROM [Posts] WHERE [AuthorId] NOT IN (1, 2, 3, 4, 5)
```

You can pass a `Query` instance to filter against a sub query

```cs
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

SqlKata try to optimize the `EXISTS` query by disregarding the selected columns and limiting the result to `1` in order to provide a consistent behavior across all database engines.


## Where Raw
The `WhereRaw` methods allows you to write anything not supported by the methods above, so it will give you the maximum flexibility.


```cs
new Query("Posts").WhereRaw("lower(Title) = ?", "sql");
```

```sql
SELECT * FROM [Posts] WHERE lower(Title) = 'sql'
```

Sometimes it's useful to wrap your table/columns by the engine identifier, this is helpful when the database is case sensitive like in PostgreSql, to do so just wrap your string with `{` and `}` and SqlKata will put the correspondent identifiers.

```cs
new Query("Posts").WhereRaw("lower({Title}) = ?", "sql");
```

In Sql Server
```sql
SELECT * FROM [Posts] WHERE lower([Title]) = 'sql'
```

In PostgreSql
```sql
SELECT * FROM "Posts" WHERE lower("Title") = 'sql'
```

