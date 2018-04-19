# Combining Multiple Queries

## Union / Except / Intersect

SqlKata allows you to combine multiple queries using one of the following available operators `union`, `intersect` and `except` by providing the following methods `Union`, `UnionAll`, `Intersect`, `IntersectAll`, `Except` and `ExceptAll`.


the above methods accept either an instance of `Query` or a labmda expression


```cs
var phones = new Query("Phones");
var laptops = new Query("Laptops");

var mobiles = laptops.Union(phones);
```

```sql
(SELECT * FROM [Laptops]) UNION (SELECT * FROM [Phones])
```


Or by using the labmda overload

```cs
var mobiles = new Query("Laptops").ExceptAll(q => q.From("OldLaptops"));
```

```sql
(SELECT * FROM [Laptops]) EXCEPT ALL (SELECT * FROM [OldLaptops])
```

## Combining Raw Expressions

You can always use the `CombineRaw` method to append raw expressions

```cs
var mobiles = new Query("Laptops").CombineRaw("union all select * from OldLaptops");
```

```sql
SELECT * FROM [Laptops] union all select * from OldLaptops
```

Off course you can use the table identifier characters `[` and `]` to instruct SqlKata to wrap the tables/columns keywords.


```cs
var mobiles = new Query("Laptops").CombineRaw("union all select * from [OldLaptops]");
```

```sql
SELECT * FROM [Laptops] union all select * from [OldLaptops]
```