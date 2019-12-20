# SqlKata
<div class="tags-container" style="margin-top: -80px">
  <span class="tag">Sql Server</span>
  <span class="tag">SQLite</span>
  <span class="tag">PostgreSql</span>
  <span class="tag">MySql</span>
  <span class="tag">Oracle</span>
  <span class="tag">Firebird</span>
</div>

## Introduction

An elegant Query Builder and Executor helps you deal with SQL queries in an elegant and predictable way.

Written in C#, the language we all love, you can check the source code on [SqlKata on Github](https://github.com/sqlkata/querybuilder)

It uses parameter binding technique to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

In addition to protection against SQL injection attacks, this technique speeds up your query execution by letting the SQL engine caches and reuses the same query plan even if the parameters are changed.


```cs
IEnumerable<Post> posts = await db.Query("Posts")
    .Where("Likes", ">", 10)
    .WhereIn("Lang", new [] {"en", "fr"})
    .WhereNotNull("AuthorId")
    .OrderByDesc("Date")
    .Select("Id", "Title")
    .GetAsync<Post>();
```

```sql
SELECT [Id], [Title] FROM [Posts] WHERE
  [Likes] > @p1 AND
  [Lang] IN ( @p2, @p3 ) AND
  [AuthorId] IS NOT NULL
ORDER BY [Date] DESC
```


## Installation
SqlKata is supported on both `net fx` and `netstandard` frameworks.

Run the following commands from your terminal to include it in your project.

Using dotnet cli
```
dotnet add package SqlKata
dotnet add package SqlKata.Execution
```

Or from the Package Manager Console

```
Install-Package SqlKata
Install-Package SqlKata.Execution
```

> **Note:** The `SqlKata.Execution` is only needed if you want add the execution support


> **Note:** The `SqlKata.Execution` works only on `netfx451` and above, or `netstandard1.3` and above

## Getting started

```cs
using SqlKata;
using SqlKata.Execution;
using System.Data.SqlClient; // Sql Server Connection Namespace

// Setup the connection and compiler
var connection = new SqlConnection("Data Source=MyDb;User Id=User;Password=TopSecret");
var compiler = new SqlServerCompiler();

var db = new QueryFactory(connection, compiler);

// You can register the QueryFactory in the IoC container

var user = db.Query("Users").Where("Id", 1).Where("Status", "Active").First();

```

Sql output
```sql
SELECT TOP(1) * FROM [Users] WHERE [Id] = @p0 AND [Status] = @p1
```
where `@p0`, `@p1` are equivalent to `1`, `"Active"` respectively.

## Compile only example

If you don't need to execute your queries, you can use SqlKata to build and compile your query to SQL string with an array of bindings. Off course no connection instance needed here.

The simplest way to get started is to create a new instance of the `Query` object by passing the table name in it.

```csharp
using SqlKata;
using SqlKata.Compilers;

// Create an instance of SQLServer
var compiler = new SqlServerCompiler();

var query = new Query("Users").Where("Id", 1).Where("Status", "Active");

SqlResult result = compiler.Compile(query);

string sql = result.Sql;
List<object> bindings = result.Bindings; // [ 1, "Active" ]
```

It will generate the following SQL string

```sql
SELECT * FROM [Users] WHERE [Id] = @p0 AND [Status] = @p1
```

>**Warning:** Don't ever use `SqlResult.ToString()` to execute your queries.

### Nightly Builds

Nightly builds are hosted on MyGet.
Add the MyGet `sqlkata` feed to your `NuGet.Config`.

```xml
<add key="sqlkata" value="https://www.myget.org/F/sqlkata/api/v3/index.json" />
```

> **Note: ** if this file is not found, create it next to your `.csproj` file

The final file should be similar to this
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="NuGet" value="https://api.nuget.org/v3/index.json" />
    <add key="sqlkata" value="https://www.myget.org/F/sqlkata/api/v3/index.json" />
  </packageSources>
</configuration>
```