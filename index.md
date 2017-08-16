# SqlKata

## Introduction

SqlKata query builder helps you to deal with SQL queries in an elegant, predictable and dynamic way.

The SqlKata query builder uses parameter binding technique to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

In addition to protection against SQL injection attacks, this technique speed up your query execution by letting the SQL engine to cache and reuses the same query plan even if the parameters are changed.


## Installation
SqlKata is hosted on nuget, run the following commands from your terminal to include it in your project.

Using dotnet core cli
```
dotnet add package SqlKata -v 1.0.0-beta-353
```

Legacy dotnet applications

```
Install-Package SqlKata -Version 1.0.0-beta-353
```


## Getting started

The simplest way to get started is to create a new instance of the `Query` object by passing the table name in it.

```cs
using SqlKata;
using SqlKata.Compilers;

// Create an instance of SQLServer
var compiler = new SqlServerCompiler();

var query = new Query("MyTable");

var result = compiler.Compile(query).Sql;

// print the compiled SQL
Console.WriteLine(result.Sql)
```

Sql output
```sql
SELECT * FROM [MyTable]
```

### Parameter bindings
To get the list of bindings 

```cs
List<object> bindings = result.Bindings
```

### Sql vs RawSql vs ToString

#### Sql
This property return the compiled SQL string with indexed parameters of the form `@p0`, `@p1` etc ...

Actually you should pass this to the database engine

#### RawSql
This is the same as the `Sql` except that the parameters are not indexed, and a `?` is used as placeholder

#### ToString()
This property should be used only for debuging and demo purposes, in this method the parameters get replaced with the placeholder just to make it easy for you if you want to log or debug the queries.

>**Note:** Don't use `ToString()` to execute your queries


