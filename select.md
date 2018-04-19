# Select

## Column
Select a single or many columns

```cs
new Query("Posts").Select("Id", "Title", "CreatedAt as Date");
```

```sql
SELECT [Id], [Title], [CreatedAt] AS [Date] FROM [Posts]
```

> **Note:** As in the above example to alias a column you should use the **as** syntax

## Sub query
Select from a sub query

```cs
var countQuery = new Query("Comments").WhereColumns("Comments.PostId", "Posts.Id").AsCount();

new Query("Posts").Select("Id").Select(countQuery, "CommentsCount");
```

```sql
SELECT [Id], (SELECT COUNT(*) AS [count] FROM [Comments] WHERE [Comments].[PostId] = [Posts].[Id]) AS [CommentsCount] FROM [Posts]
```

## Raw
Your friend when you need the full freedom

```cs
new Query("Posts").Select("Id").SelectRaw("count(1) over(partition by AuthorId) as PostsByAuthor")
```

```sql
SELECT [Id], count(1) over(partition by AuthorId) as PostsByAuthor FROM [Posts]
```

## Identify columns and tables inside Raw
You can wrap your identifier inside `[` and `]` so they get recognized by SqlKata as an identifier, so we can rewrite the same example above as


```cs
new Query("Posts").Select("Id").SelectRaw("count(1) over(partition by [AuthorId]) as [PostsByAuthor]")
```

Now `AuthorId` and `PostsByAuthor` get wrapped with the compiler identifiers, this is helpful especially for case sensitive engine like PostgreSql.

In SqlServer

```sql
SELECT [Id], count(1) over(partition by [AuthorId]) as [PostsByAuthor] FROM [Posts]
```

In Postgres

```sql
SELECT "Id", count(1) over(partition by "AuthorId") as "PostsByAuthor" FROM "Posts"
```

In MySql

```sql
SELECT `Id`, count(1) over(partition by `AuthorId`) as `PostsByAuthor` FROM `Posts`
```