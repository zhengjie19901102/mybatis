## Oracle批量插入数据的三种方式
`第一种:`

```sql
begin
	insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
	insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
	insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
	...
end;
```

`第二种:`

```sql
insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
insert into tableName(column1, column2, column3...) values(value1,value2,value3...);
```

`第三种(利用中间表):`

```sql
insert into tableName(column1(主键),column2,column3...)
	select tableNames_seq.nextval,,column2,column3... from (
	select value1 column2,value2 column3,value3 column4 from dual
	union
	select value1 column2,value2 column3,value3 column4 from dual
	union
	select value1 column2,value2 column3,value3 column4 from dual
	union
	select value1 column2,value2 column3,value3 column4 from dual
)
```