# Update Data

SqlKata provides the following methods to help with updating/inserting/deleting against your database:

 - `Update()`
 - `UpdateAsync()`

 - `Insert()`
 - `InsertAsync()`

 - `Delete()`
 - `DeleteAsync()`


```cs
var db = new QueryFactory(connection, new SqlServerCompiler());
```

## Update Existing Data

```cs

var data = new Dictionary<string, object> {
    { "Price", 18 },
    { "Status", "active" }
};

int affected = db.Query("Books").Where("Id", 1).Update(data);
```

## Insert One Record

```cs

var book = new Dictionary<string, object> {
    { "Title", "Introduction to C#" }
    { "Price", 18 },
    { "Status", "active" }
};

int affected = db.Query("Books").Insert(book);

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