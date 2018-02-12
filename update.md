# Insert, Update and Delete

> **Note:** Currently, the following clauses: `using`, `order by`, `group by`, `having`, `join`, `limit`, `offset` and `distinct` are totally ignored for the **Insert**, **Update** and **Delete** statements.

## Insert
```cs
var data = new Dictionary<string, object> {
    {"Title", "Toyota Kata"},
    {"CreatedAt", "August 4, 2009"},
    {"Author", "Mike Rother"}
};

var query = new Query("Posts")
    .AsInsert(data);
```

```sql
INSERT INTO [Book] ([Name], [CreatedAt], [Author]) VALUES (?, ?, ?)
```

### Insert from Query

You can also insert records for the result of another Select Query.

```cs
var cols = new [] { "Id", "Name", "Address" };
new Query("ActiveUsers").AsInsert(cols, new Query("Users").Where("Active", 1));
```

```sql
INSERT INTO [ActiveUsers] ([Id], [Name], [Address]) SELECT * FROM [Users] WHERE [Active] = 1
```

## Update

```cs
var data = new Dictionary<string, object> {
    {"AuthorId", 10}
};

var query = new Query("Posts").WhereNull("AuthorId").AsUpdate(data);
```

```sql
UPDATE [Posts] SET [AuthorId] = 10 WHERE [AuthorId] IS NULL
```

## Delete

```cs
var query = new Query("Posts").Where("Date", ">", DateTime.UtcNow.AddDays(-30)).AsDelete();
```

```sql
DELETE FROM [Posts] WHERE [Date] > ?
```