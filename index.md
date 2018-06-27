# SqlKata

## Introduction

An elegant Query Builder and Executor helps you deal with SQL queries in an elegant and predictable way.

Written in C#, the language we all love, you can check the source code on [SqlKata on Github](https://github.com/sqlkata/querybuilder)

It uses parameter binding technique to protect your application against SQL injection attacks. There is no need to clean strings being passed as bindings.

In addition to protection against SQL injection attacks, this technique speeds up your query execution by letting the SQL engine caches and reuses the same query plan even if the parameters are changed.

```cs

bool havingCommentsOnly = Config.Get('OnlyWithComments');

IEnumerable<Post> posts = await db.Query("Posts")
    .Where("Likes", ">", 10)
    .WhereIn("Lang", new [] {"en", "fr"})
    .WhereNotNull("AuthorId")
    .When(havingCommentsOnly, q => {

        // this will be executed only if `havingCommentsOnly` is true
        q.WhereExists(q => q.From("Comments").WhereColumns("PostId", "Posts.Id"))
    })
    .OrderByDesc("Date")
    .Select("Id", "Title")
    .GetAsync<Post>();
```

```sql
SELECT [Id], [Title] FROM [Posts]
WHERE [Likes] > @p1
  AND [Lang] IN ( @p2, @p3 )
  AND [AuthorId] IS NOT NULL
  AND EXISTS (
    SELECT TOP 1 FROM [Comments] WHERE [PostId] = [Posts].[Id]
  )
ORDER BY [Date] DESC
```


## Installation
SqlKata is supported on both `net` and `netstandard`.

Until we reach the stable version, the NuGet Packages will be hosted on MyGet.

### Add SqlKata MyGet feed to your project

One way to do so is by adding the MyGet feed to your `NuGet.Config`.

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

### Restore packages

Run the following commands from your terminal to include it in your project.

Using dotnet cli
```
dotnet add package SqlKata --version 1.0.0-beta-484
```

Or from the Package Manager Console

```
Install-Package SqlKata -Version 1.0.0-beta-484
```

## Getting started

The simplest way to get started is to create a new instance of the `Query` object by passing the table name in it.

```cs
using SqlKata;
using SqlKata.Compilers;

// Create an instance of SQLServer
var compiler = new SqlServerCompiler();

var query = new Query("Users").Where("Id", 1).Where("Status", "Active");

SqlResult result = compiler.Compile(query);

string sql = result.Sql; // SELECT * FROM [Users] WHERE [Id] = ? AND [Status] = ?
List<object> bindings = result.Bindings; // [ 1, "Active" ]
```

Sql output
```sql
SELECT * FROM [Users] WHERE [Id] = ? AND [Status] = ?
```

### Sql vs RawSql vs ToString

#### result.Sql
This property returns the compiled SQL string with indexed parameters of the form `@p0`, `@p1` etc ...

Actually you should pass this to the database engine

#### result.RawSql
This is the same as the `Sql` except that the parameters are not indexed, and a `?` is used as placeholder

#### result.ToString()
This property should be used only for debugging and demo purposes, in this method the parameters get replaced with the placeholder just to make it easy for you if you want to log or debug the queries.

>**Warning:** Don't use `ToString()` to execute your queries


