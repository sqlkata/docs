# Join

## Basic Join

To apply an inner join use the `Join` method

```cs
var query = new Query("Posts").Join("Authors", "Authors.Id", "Posts.AuthorId");
```

The methods `LeftJoin`, `RightJoin` and `CrossJoin` have the same signature as the `Join` method.

```sql
SELECT * FROM [Posts] INNER JOIN [Authors] ON [Authors].[Id] = [Posts].[AuthorId]
```

The 4th parameter is optional and default to `=`, pass any other operator to override the join operator.

```cs
var query = new Query("Posts").Join("Comments", "Comments.Date", "Posts.Date", ">");
```

## Join with a Sub Query

```cs
var topComments = new Query("Comments").OrderByDesc("Likes").Limit(10);

var posts = new Query("Posts").LeftJoin(
    topComments.As("TopComments"), // Don't forget to alias the sub query
    j => j.On("TopComments.PostId", "Posts.Id")
);
```

```sql
SELECT * FROM [Posts] LEFT JOIN (
    SELECT TOP (10) * FROM [Comments] ORDER BY [Likes] DESC
) AS [TopComments] ON ([TopComments].[PostId] = [Posts].[Id])
```

> **Warning:** Always alias your sub queries with the `As` method

## Advanced conditions

In some advanced cases you may need to apply some constraints on the join clause.

```cs
var comments = new Query("comments").LeftJoin("Posts", j => 
    j.On("Posts.Id", "Comments.Id").WhereNotNull("Comments.AuthorId")
);
```

```sql
SELECT * FROM [comments] LEFT JOIN [Posts] ON ([Posts].[Id] = [Comments].[Id] AND [Comments].[AuthorId] IS NOT NULL)
```