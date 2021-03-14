# Having

## Having

```cs
//:playground
var commentsCount = new Query("Comments").Select("PostId").SelectRaw("count(1) as Count").GroupBy("PostId");

var query = new Query().From(commentsCount).Having("Count", ">", 100);
```

```sql
SELECT * FROM (SELECT [PostId], count(1) as Count FROM [Comments] GROUP BY [PostId]) HAVING [Count] > 100
```



## HavingRaw

```cs
//:playground
var query = new Query("Comments").Select("PostId").SelectRaw("count(1) as Count").GroupBy("PostId").HavingRaw("count(1) > 50");
```

```sql
SELECT [PostId], count(1) as Count FROM [Comments] GROUP BY [PostId] HAVING count(1) > 50
```

## Nested Having
If you want to nest the having conditions, you have to use the `WhereX` methods instead, the compiler will look for the `Where`s conditions to compile the nested statements.

```cs
//:playground
var query = new Query("Comments")
    .Select("PostId")
    .SelectRaw("count(1) as Count")
    .GroupBy("PostId")
    .Having(q => q.Where("Count", ">", 50).OrWhere("Count", "<", 20));
```

```sql
SELECT [PostId], count(1) as Count FROM [Comments] GROUP BY [PostId] HAVING ([Count] > 50 OR [Count] < 20)
```