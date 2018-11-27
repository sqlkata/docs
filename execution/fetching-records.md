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

To paginate your data, use the `Paginate(pageNumber, perPage?)` method instead of `Get`.

the `Paginate` method accept two parameters, the `page` number (1 based) and an optional `perPage` that defaults to 25, and return an instance of type `PaginationResult`.

The `PaginationResult` expose the `Each` property that implements the `Enumerable` interface so you can safely iterate over the returned data.


```cs
// users is of type `PaginationResult`
var users = query.Paginate(1, 10);

foreach(var user in users.Each)
{
    Console.WriteLine($"Id: {user.Id}, Name: {user.Name}");
}
```

### Next and Previous

You can call the `Next` and `Previous` methods to get the Next/Previous page respectively.

```cs

var page1 = query.Paginate(1);

foreach(var item in page1.Each)
{
    // print items in the first page
}

var page2 = page1.Next(); // same as query.Paginate(2)

foreach(var item in page2.Each)
{
    // print items in the 2nd page
}

```

### Next and Previous Queries

Sometimes you may need to access the underlying queries for the next and previous methods. Use the `NextQuery` and `PreviousQuery` respectively in this case.

Accessing the queries can sometime more beneficial if you want more control, i.e. adding additional constraint.

```cs

var currentPage = query.Paginate(1, 10);

foreach(var item in currentPage.Each)
{
    // print all books in the first page
}

var books_2 = currentPage.NextQuery().WhereTrue("IsPublished").Get();

foreach(var item in books_2.Each)
{
    // print published books only in page 2
}

```

### Looping over all records example

This example may not be used in real cases, use the **Chunk** method instead if you need such functionality

```cs
var currentPage = db.Query("Books").OrderBy("Date").Paginate(1);

while(currentPage.HasNext)
{
    Console.WriteLine($"Looping over the page: {currentPage.Page}");

    foreach(var book in currentPage.Each)
    {
        // process book
    }

    currentPage = currentPage.Next();
}
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