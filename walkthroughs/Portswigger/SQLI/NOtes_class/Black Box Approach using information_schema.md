	# When you have no prior knowledge of database 
we will explore the database as a black box approach now , as what happens in the real life scenario:

We know that the database contains the records of it's records , those records are stored in the `information_schema` table 

we will use this table to leak info about the database:

### step 1
***finding the databases present***

by using the query:
```
SELECT schema_name AS database_name FROM information_schema.schemata;
```

we get all the DB that are currently being hosted on that mysql platform

### step 2
***finding the tables from the desired database***
now since we got the database names , we need now the tables it has so that we can get the desired info we need , we can achieve it via:
```
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'database_name';
```

### step 3
***Explore the table structure***

Get to know the columns of the table via this query:
```
SELECT column_name, data_type
FROM information_schema.columns
WHERE table_schema = 'database_name'
  AND table_name = 'table_name';
```


Now just select the data you want via simple sql query:
```
SELECT * FROM 'database_name'.'table_name'
```



![[Pasted image 20230616190221.png]]



![[Pasted image 20230616185951.png]]


![[Pasted image 20230616190058.png]]
![[Pasted image 20230616190133.png]]

![[Pasted image 20230616190302.png]]

![[Pasted image 20230616190329.png]]

![[Pasted image 20230616191958.png]]

