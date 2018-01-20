# Setup Your Project

In order to execute the queries, you have to create instances of `XQuery`, that supports the execution methods.

## XQuery class

`XQuery` constructor requires two mandatory parameters, the Database connection (of type `IDbConnection`) instance and the `Compiler` instance.

> **Note: ** `XQuery` extends the `Query` class and support all methods supported by the later

```cs
var connection = new MySqlConnection(
    "Host=localhost;Port=3306;User=user;Password=secret;Database=Users;SslMode=None"
);

var compiler = new MySqlCompiler();

var users = new XQuery(connection, compiler).From("Users").Limit(10).Get();
```

While this approach works as expected but it becomes easily cumbersome to use on big projects, since you have to keep reference for those required parameters, and on environments using DI containers.

In these cases it's recommended to use the `QueryFactory` factory class.


## QueryFactory

As it's name imply, it helps in creating Executable Queries (`XQuery`) instances, without the hassle of keeping references for the `IDbConnection` and the `Compiler` instances.


```cs

var connection = new MySqlConnection(
    "Host=localhost;Port=3306;User=user;Password=secret;Database=Users;SslMode=None"
);

var db = new QueryFactory(connection, new MySqlCompiler());

// From now on, you can use the `factory.Create()` method
var users = db.Query().From("Users").Get();

// or more simpler
var users = db.Query("Users").Get();
```

### Asp.Net Core DI Container

On Asp.Net core projects, you can setup your container to resolve the needed instances of the factory.


In `Startup.cs`

```cs
services.Add<QueryFactory>(() => {

    // In real life you may read the configuration dynamically
    var connection = new MySqlConnection(
        "Host=localhost;Port=3306;User=user;Password=secret;Database=Users;SslMode=None"
    );

    var compiler = new MySqlCompiler();


    return new QueryFactory(connection, compiler);

});
```

and in the `HomeController.cs`

```cs

using SqlKata;
using SqlKata.Execution;

public class HomeController {

    private readonly QueryFactory db;

    public HomeController(QueryFactory db) {

        this.db = db;

    }

    public IActionResult Index() {

        // Here books is of type `IEnumerable<dynamic>`
        var books = db.Query("Books").Where("IsPublished", true).Get();

        // or `IEnumerable<Book>` if using the Generic overload
        var books = db.Query("Books").Where("IsPublished", true).Get<Book>();

        return Ok(books);

    }

}
```