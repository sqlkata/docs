# Fetching Records

SqlKata provides the following methods to help executing your queries:

 - `Get()`
 - `First()`
 - `FirstOrDefault()`
 - `Paginate()`
 - `Chunk()`

## Retrieving Records

By default calling the `Get` method returns an `IEnumerable<dynamic>`, giving you the max flexibility

```cs
var db = new QueryFactory(connection, new SqlServerCompiler());

IEnumerable<dynamic> users = db.Query("Users").Get();
```

However you can use the generic overload when you prefer strong types instead

```cs
IEnumerable<User> users = db.Query("Users").Get<User>();
```

## Getting One Record

use the `First` or `FirstOrDefault` to get the first record of the query

```cs
var book = db.Query("Books").Where("Id", 1).First<Book>();
```

> **Note: ** The `First` and `FirstOrDefault` add the `Limit(1)` clause implicitly to the query, so there is no need to add it by yourself.

## Data Pagination

SqlKata make it easy to paginate your data, just call the `Paginate` method instead of `Get`.

the `Paginate` method accept two parameters, the `page` number (1 based) and an optional `perPage` that defaults to 25, and return an instance of type `PaginationResult`.

`PaginationResult` implements the `Enumerable` interface so you can safely iterate over the returned data.


```cs
// users is of type `PaginationResult`
var users = query.Paginate(1, 10);

foreach(var user in users)
{
    Console.WriteLine($"Id: {user.Id}, Name: {user.Name}");
}
```

### Next and Previous
You can get the next or previous page by calling the `NextQuery` and `PreviousQuery` respectively.

```cs

var page1 = query.Paginate(1, 10);

// same as query.Paginate(2, 10)
var page2Query = page1.NextQuery();

```

To get the data directly you can call the `Next` and `Previous` methods respectively.

```cs

var page1 = query.Paginate(1);

// page2 is of type IEnumerable<dynamic>
var page2 = page1.Next();

```

## Data Chunks
Sometimes you may want to retrieve data in chunks to prevent loading the whole table one time in memory, for this you can use the `Chunk` method.

This is useful in situations where you have thousands of records.

```cs
query.Chunk(100, (rows, page) => {

    Console.WriteLine($"Fetching page: {page}");

    foreach(var row in rows)
    {
        // do something with row
    }

});
```

To stop retrieving chunks simply use the `Chunk(int chunkSize, Func<IEnumerable<dynamic>, int, bool> func)` overload and return false from the invoked action

```cs
query.Chunk(100, (rows, page) => {

    // process rows

    if(page == 3) {

        // stop retrieving other chunks
        return false;

    }

    // return true to continue
    return true;

});
```

## Execute Raw Statements
If you want to execute free raw statements, use the `QueryFactory.Select` and `QueryFactory.Statement` methods.

```cs
var users = db.Select("exec sp_get_users_by_date @date", new {date = DateTime.UtcNow});
```

`QueryFactory.Statement` allows you to execute arbitrary statements like `truncate table`, `create database`, etc ...

```cs
db.Statement("truncate table Users");
```