# Select

## Column
Select a single or many columns

```cs
//:playground
new Query("Posts").Select("Id", "Title", "CreatedAt as Date");
```

```sql
SELECT [Id], [Title], [CreatedAt] AS [Date] FROM [Posts]
```

> **Note:** You can use the **as** keyword to alias a column in the select list

## Sub query
Select from a sub query

```cs
//:playground
var countQuery = new Query("Comments").WhereColumns("Comments.PostId", "=", "Posts.Id").AsCount();

var query = new Query("Posts").Select("Id").Select(countQuery, "CommentsCount");
```

```sql
SELECT [Id], (SELECT COUNT(*) AS [count] FROM [Comments] WHERE [Comments].[PostId] = [Posts].[Id]) AS [CommentsCount] FROM [Posts]
```

## Raw
Your friend when you need the full freedom

```cs
//:playground
new Query("Posts").Select("Id").SelectRaw("count(1) over(partition by AuthorId) as PostsByAuthor")
```

```sql
SELECT [Id], count(1) over(partition by AuthorId) as PostsByAuthor FROM [Posts]
```

## Identify columns and tables inside Raw
You can wrap your identifier inside `[` and `]` so they get recognized by SqlKata as an identifier, so we can rewrite the same example above as


```cs
//:playground
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

## Expanding Columns Expression (Braces Expansions)
Starting **v1.1.2**, you can use the Braces Expansions feature, to select multiple columns at the same time. This will allow you to write the same query in a more compact way.

```cs
//:playground
new Query("Users")
    .Join("Profiles", "Profiles.UserId", "Users.Id")
    .Select("Users.{Id, Name, LastName}", "Profiles.{GithubUrl, Website}")
```

Same as writing

```cs
//:playground
new Query("Users")
    .Join("Profiles", "Profiles.UserId", "Users.Id")
    .Select("Users.Id", "Users.Name", "Users.LastName", "Profiles.GithubUrl", "Profiles.Website")
```

```sql
SELECT [Users].[Id], [Users].[Name], [Users].[LastName], [Profiles].[GithubUrl], [Profile].[Website] FROM [Users]
    JOIN [Profiles] ON [Profiles.UserId] = [Users].[Id]
```