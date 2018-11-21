title: 流行关系型数据的SQL对比
date: 2018-10-28 14:57:34
desc: 
tags: [技术] 
---

以MySQL为参考标准

<!-- more -->

## show databases

* SQLServer

```
select name
from sys.databases;
```

## show tables

* SQLServer

```
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
where TABLE_TYPE = 'BASE TABLE';
/
select name, xtype
FROM sysobjects
WHERE (xtype = 'U')
```

## desc table

* SQLServer
```
select B.name AS column_name, D.name AS column_type, C.value AS column_description
FROM sys.tables A
       INNER JOIN sys.columns B ON B.object_id = A.object_id
       LEFT JOIN sys.extended_properties C ON C.major_id = B.object_id AND C.minor_id = B.column_id
       inner join sys.types D on B.system_type_id = D.user_type_id
WHERE A.name = 'p_Template'
order by B.column_id;
```