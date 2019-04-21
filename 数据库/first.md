## MySQL理论考试

1. 简述char与varchar的区别

   答： 分别是定长与变长.

   ​	以char(M)为例, 可以存储0-M个字符,存储不够M个字符, 仍然占据M个字符的宽度.(不够M个,右侧用空格补齐).

   ​	varchar(M),可以存储0-M个字符,但需要1-2个额外的字节,来标注此字段具体的大小.

2. 事务的4个特性

   答： ACID, 

   ​	原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）、持久性（Durability）

3. 简要说明mysql左连接/右连接的区别

    答：左连接,取左表数据,和右表相关数据,如右表无相关数据,以NULL补齐. 右边接反之.

4. 两张表city表和province表。分别为城市与省份的关系表

   \> select * from city;

   | id   | name | province_id | GDP  |
   | ---- | ---- | ----------- | ---- |
   | 1    | 广州        | 1    | 27   |
   | 2    | 深圳        | 1    | 35   |
   | 3    | 北京        | 2    | 28   |
   | 4    | 上海        | 3    | 46   |

    \> select * from province;

   | id   | name |
   | ---- | ---- |
   | 1    | 广东 |
   | 2    | 北京 |
   | 3    | 上海 |

    

   4.1 写出provice表的建表语句

    ```sql
   create table provice (
       id int primary key auto_increment,
       name varchar(50) not null
   )charset utf8;
    ```

   

    4.2 查出GDP排名前3高的城市

    ```mysql
   select * from city order by GDP  limit 0,3;
    ```

   

    4.3 写一条sql语句关联两个表，实现：显示出字段：城市id，城市名，省份名

    ```sql
   select city.*,privince.name from cityl left join province on city.province_id=province.id;
    ```

   

    4.4 统计每个省份有多少个城市

    ```sql
   select province_id, count(*) from city group by province_id;
    ```

   

    4.5 统计每个省的GDP(即省下的所有城市的GDP之和)

    ```sql
   select province_id,sum(gdp) from city group by province_id;
    ```

   


5. 设计一个简化的bbs系统：多用户，可注册，可发表主题，回复，系统分不同的版块等。

   把所需的表和字段写出来.(提示,用户表user,主题表post,回复表reply,版块表board)

 user 用户表:

| uid  | name | password      |
| ---- | ---- | ------------- |
| 97   | poly | md5(‘123456’) |

board 版块表

| bid  | bname    |
| ---- | -------- |
| 4    | 人生哲理 |

post 主题表:

| pid  | title    | content    | pubtime | uid  | bid  |
| ---- | -------- | ---------- | ------- | ---- | ---- |
| 323  | 今天有雨 | 真大的雷声 | 1345678 | 97   | 4    |

reply 回复表

| rid  | uid  | pid  | content  |
| ---- | ---- | ---- | -------- |
| 655  | 8    | 323  | 你说的对 |

6. 设计支持无线级分类的最简表结构

 ```sql
create table cat (
cid int primary key auto_increment,
name char(5) not null default ‘’,
pid int
)charset utf8;
 ```

7. 如何查看SQL语句的执行效率？

    答： explain 查看索引的使用效果

   ​	show profile for query N.(使用前要先 set profiling=1)

   

8. 关系数据库中，索引的作用主要有哪些，一般什么情况下需要建索引？

   并简述索引都有哪几种类型，有何区别？

   答：提高查询速度,有利于排序和分组. (排序和分组如用不上索引,则会产生临时表和filesort的过程)

   ​	根据业务逻辑,分析列查询的频度和顺序, 建立索引和复合索引.

   ​	主键索引, 唯一索引,普通索引,全文索引,复合索引.

   ​	普通索引(key),

   ​	全文索引(fulltext key)--->中文环境下基本无效，一般用第三方方案如sphinx(中文分词)

   ​	多列索引：key xm(xing,ming)用xing和ming两个字段做索引

   ​	冗余索引：在某个字段上有多个索引，如 key xm(xing,ming) ,key m(ming),有两个 就是冗余索引

   

9. 在使用多列索引或建立多列索引时，我们一般要遵循“最左前缀原则”。请简单说明“最左前缀原则”。

   答： 针对单列索引, 左边准确而右边模糊,可以用到索引,反之则不可以.

   ​	如 where name like ‘poly%’,可以用到, 而”%poly”则不用到.

   ​	针对多列索引, 左边的列用到索引后,右侧的列才有可能用到索引.

   ​	例 index(a,b,c), where a=? and b=? ,b列索引会用到.

   ​	如果直接 where b=?, 因为a列没用索引,所以b索引,用不到.