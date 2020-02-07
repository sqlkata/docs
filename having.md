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