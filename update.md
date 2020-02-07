# Insert, Update and Delete

> **Note:** Currently, the following clauses: `using`, `order by`, `group by`, `having`, `join`, `limit`, `offset` and `distinct` are totally ignored for the **Insert**, **Update** and **Delete** statements.

## Insert
```cs
//:playground
var query = new Query("Books").AsInsert(new {
    Title = "Toyota Kata",
    CreatedAt = new DateTime(2009, 8, 4),
    Author = "Mike Rother"
});
```

```sql
INSERT INTO [Books] ([Name], [CreatedAt], [Author]) VALUES ('Toyota Kata', '2009-08-04 00:00:00', 'Mike Rother')
```


> **Note: ** While executing the query you can get the inserted **id** using the `InsertGetId()` method


### Insert Many
you can use the insert many overload to insert multiple records

```cs
//:playground
var cols = new [] {"Name", "Price"};

var data = new [] {
    new object[] { "A", 1000 },
    new object[] { "B", 2000 },
    new object[] { "C", 3000 },
};

var query = new Query("Products")
    .AsInsert(cols, data);
```

```sql
INSERT INTO [Products] ([Name], [Price]) VALUES ("A", 1000), ("B", 2000), ("C", 3000)
```

### Insert from Query

You can also insert records for the result of another Select Query.

```cs
//:playground
var cols = new [] { "Id", "Name", "Address" };
new Query("ActiveUsers").AsInsert(cols, new Query("Users").Where("Active", 1));
```

```sql
INSERT INTO [ActiveUsers] ([Id], [Name], [Address]) SELECT * FROM [Users] WHERE [Active] = 1
```

## Update

```cs
//:playground
var query = new Query("Posts").WhereNull("AuthorId").AsUpdate(new {
    AuthorId = 10
});
```

```sql
UPDATE [Posts] SET [AuthorId] = 10 WHERE [AuthorId] IS NULL
```

## Delete

```cs
//:playground
var query = new Query("Posts").Where("Date", ">", DateTime.UtcNow.AddDays(-30)).AsDelete();
```

```sql
DELETE FROM [Posts] WHERE [Date] > ?
```