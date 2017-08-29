# Common Table Expression
A common table expression (CTE) can be thought of as a temporary result set, it has some advantages over the sub query approach.

In Sql, usually CTE is represented as a `with` clause.

## With
To add a CTE to your query simply use the `With` method.

```cs
var activePosts = new Query("Comments")
    .Select("PostId")
    .SelectRaw("count(1) as Count")
    .GroupBy("PostId")
    .HavingRaw("count(1) > 100");

var query = new Query("Posts")
    .With("ActivePosts", activePosts) // now you can consider ActivePosts as a regular table in the database
    .Join("ActivePosts", "ActivePosts.PostId", "Posts.Id")
    .Select("Posts.*", "ActivePosts.Count")
```

```sql
WITH [ActivePosts] AS (
    SELECT [PostId], count(1) as Count FROM [Comments] GROUP BY [PostId] HAVING count(1) > 100
)
SELECT [Posts].*, [ActivePosts].[Count] FROM [Posts] INNER JOIN [ActivePosts] ON [ActivePosts].[PostId] = [Posts].[Id]
```

## WithRaw
You can use the `WithRaw` method if you want to pass an Sql Expression.

```cs
var query = new Query("Posts")
    .WithRaw("ActivePosts", "select PostId, count(1) as count from Comments having count(1) > ?", new [] {50}) // now you can consider ActivePosts as a regular table in the database
    .Join("ActivePosts", "ActivePosts.PostId", "Posts.Id")
    .Select("Posts.*", "ActivePosts.Count")
```

```sql
WITH [ActivePosts] AS (
        select PostId, count(1) as count from Comments having count(1) > 50
)
SELECT [Posts].*, [ActivePosts].[Count] FROM [Posts] INNER JOIN [ActivePosts] ON [ActivePosts].[PostId] = [Posts].[Id]
```

As in the example above, you can pass an `IEnumerable<object>` to pass custom binding.