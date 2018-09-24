# Update Data

SqlKata provides the following methods to help with updating/inserting/deleting against your database:

 - `Update()`
 - `Insert()`
 - `InsertGetId()`
 - `Delete()`

```cs
var db = new QueryFactory(connection, new SqlServerCompiler());
```

## Update Existing Data

```cs
int affected = db.Query("Books").Where("Id", 1).Update(new {
    Price = 18,
    Status = "active",
});
```

## Insert One Record

```cs
int affected = db.Query("Books").Insert(new {
    Title = "Introduction to C#",
    Price = 18,
    Status = "active",
});

```

## Insert One Record and get the Inserted Id

In version 1.1.0 we've added the ability to get the inserted id, in the database.

```cs
var id = db.Query("Books").InsertGetId<int>(new {
    Title = "Introduction to Dart",
    Price = 0,
    Status = "active"
});
```

> **Note:** Currently this method is able to get the **Id** for single insert statements. Multiple records is not supported yet.

## Insert Multiple Record

```cs

var cols = new [] {"Name", "Price"};

var data = new [] {
    new object[] { "A", 1000 },
    new object[] { "B", 2000 },
    new object[] { "C", 3000 },
};

db.Query("Products").Insert(cols, data);

```

## Insert From Existing Query

```cs

var articlesQuery = new Query("Articles").Where("Type", "Book").Limit(100);
var columns = new [] { "Title", "Price", "Status" };

int affected = db.Query("Books").Insert(columns, articlesQuery);

```

## Delete

```cs
int affected = db.Query("Books").Where("Status", "inactive").Delete();
```