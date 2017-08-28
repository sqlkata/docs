String Operations
=================

SqlKata Query Builder provides `WhereStarts`, `WhereEnds`, `WhereContains` and `WhereLike` methods to deal with string like types.

By default all string operations are case insensitive, by applying the database `LOWER()` and converting the value provided to lowercase.

To override this behavior you can pass a boolean `true` to the last parameter `caseSensitive` of these methods.


```cs
new Query("Posts").WhereEnds("Title", "Book")
```

```sql
SELECT * FROM [Posts] WHERE LOWER([Title]) LIKE '%book'
```

Using the case sensitive overload

```cs
new Query("Posts").WhereStarts("Title", "Book", true)
```

```sql
SELECT * FROM [Posts] WHERE [Title] LIKE Book%
```

Using the native `WhereLike` method

```cs
new Query("Posts").WhereLike("Title", "Book")
```

```sql
SELECT * FROM "Posts" WHERE LOWER("Title") LIKE 'book'
```

> **Note:** in `WhereLike` method, you have to put the wildcard `%` by yourself