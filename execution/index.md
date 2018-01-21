# Query Execution

SqlKata provide an easy way to execute your queries, by using the famous package
[Dapper](https://github.com/StackExchange/Dapper).

## Installation
To be able to execute queries, you have to install the `SqlKata.Execution` package

```sh
dotnet add package SqlKata --version 1.0.0-beta-411
dotnet add package SqlKata.Execution --version 1.0.0-beta-411
```

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
dotnet add package dotnet add package Npgsql
```

### MySql

For MySql install [MySql.Data](https://www.nuget.org/packages/MySql.Data/8.0.9-dmr) package

```sh
dotnet add package MySql.Data --version 8.0.9-dmr
```

## Dev Builds
Currently SqlKata Nuget packages are published directly on Nuget, to access the dev builds you can add the MyGet feed to your `Nuget.Config` file, one way to do so is by adding the following to your `NuGet.Config` file.

> **Note: ** if this file is not found, create it next to your `.csproj` file

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="sqlkata" value="https://www.myget.org/F/sqlkata/api/v3/index.json" />
    <add key="NuGet" value="https://api.nuget.org/v3/index.json" />
  </packageSources>
</configuration>
```