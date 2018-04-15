# Query Logging

SqlKata allows you to log your queries by implementing the `Logger` function on the `QueryFactory` class.

```cs
var db = new QueryFactory(connection, new SqlServerCompiler());

// Log the compiled query to the console
db.Logger = compiled => {
    Console.WriteLine(compiled.ToString());
};

var users = db.Query("Users").Get();
```

Will print to the console
```sh
> SELECT * FROM [Users]
```