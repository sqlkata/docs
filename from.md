# From

## Table or View
The `Query` constructor take an optional parameter to set the `from` clause

```cs
new Query("Posts");
``` 

Or you can use the `From` method to set it

```cs
new Query().From("Posts");
```

```sql
SELECT * FROM [Posts]
```

### Alias
To alias the table you should use the `as` syntax

```cs
new Query("Posts as p")
```

```sql
SELECT * FROM [Posts] AS [p]
```

