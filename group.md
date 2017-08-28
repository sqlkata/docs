# Grouping

## GroupBy

```cs
var query = new Query("Comments")
                .Select("PostId")
                .SelectRaw("count(1) as count")
                .GroupBy("PostId");
```

```sql
SELECT [PostId], count(1) as count FROM [Comments] GROUP BY [PostId]
```

## GroupByRaw

```cs
var query = new Query("Companies")
    .Select("Profit")
    .SelectRaw("COUNT(*) as count")
    .GroupByRaw("Profit WITH ROLLUP");
```

In PostgreSql

```sql
SELECT "Profit", COUNT(*) as count FROM "Companies" GROUP BY Profit WITH ROLLUP
```