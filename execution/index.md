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