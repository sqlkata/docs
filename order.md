# Order

## OrderBy

```cs
var query = new Query("Comments").OrderBy("Date").OrderByDesc("Name");
```

```sql
SELECT * FROM [Comments] ORDER BY [Date], [Name] DESC
```



## OrderByRaw

```cs
var query = new Query("Comments").OrderByRaw("{Likes} DESC NULLS LAST")
```

In PostgreSql

```sql
SELECT * FROM "Comments" ORDER BY "Likes" DESC NULLS LAST
```