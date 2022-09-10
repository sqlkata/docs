# Query Execution

SqlKata provide an easy way to execute your queries, by using the famous package
[Dapper](https://github.com/StackExchange/Dapper).

## Installing Database Providers
In order to execute the queries, you need to install the needed Drivers for each Database Provider.

### Sql Server
For Sql Server install [System.Data.SqlClient](https://www.nuget.org/packages/System.Data.SqlClient/) package

```sh
dotnet add package System.Data.SqlClient
```

### Postgre Sql

For Postgre Sql install [Npgsql](http://www.npgsql.org/) package

```sh
dotnet add package Npgsql
```

### MySql

For MySql install [MySql.Data](https://www.nuget.org/packages/MySql.Data) package

```sh
dotnet add package MySql.Data
```

```cs
var host = "local";
var user = "sa";
var database = "healthnbeautyapp";
var password = "secret";

var cs = $"server={host};user={user};database={dbName};password={password}";

var connection = new MySqlConnection(cs);

var db = new QueryFactory(connection, new MySqlCompiler());
```

### SQLite

For SQLite install `System.Data.SQLite.Core` package

```sh
dotnet add package System.Data.SQLite.Core
```


```cs
var cs = $"Data Source=file:mydatabase.db";

var connection = new SqliteConnection(cs);

var db = new QueryFactory(connection, new SqliteCompiler());
```

or if you want to use in-memory database for testing purposes

```cs
var cs = $"Data Source=file::memory:;Cache=Shared";

var connection = new SqliteConnection(cs);

var db = new QueryFactory(connection, new SqliteCompiler());
```