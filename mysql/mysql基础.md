### 多表查询
##### 表连接
在 MySQL 中，表连接（JOIN）是用于将多个表中的数据相关联的操作。常见的表连接类型包括：

1. 内连接（<font color=#99CCFF style=" font-weight:bold;">JOIN</font>）：内连接根据两个表之间的共同值将它们关联起来，只返回符合连接条件的行。如果两个表中没有匹配的行，则不返回任何结果。

2. 左连接（<font color=#99CCFF style=" font-weight:bold;">LEFT JOIN 或 LEFT OUTER JOIN</font>）：左连接会返回左表中的所有行，以及右表中与左表匹配的行。如果右表中没有匹配的行，则返回 NULL 值。

3. 右连接（RIGHT JOIN 或 RIGHT OUTER JOIN）：右连接与左连接相反，会返回右表中的所有行，以及左表中与右表匹配的行。如果左表中没有匹配的行，则返回 NULL 值。
<font color=#99CCFF style=" font-weight:bold;">分为内连接和外连接,内连接返回两个表关联匹配的行 , 外连接 分左表为主的左连接和以右表为主的右连接</font>
