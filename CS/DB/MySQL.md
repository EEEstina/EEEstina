# MySQL

1. 什么是内连接、外连接、交叉连接、笛卡尔积呢？
MySQL 中的连接是通过两个或多个表之间的列进行关联，从而获取相关联的数据。连接分为内连接、外连接、交叉连接。

①、内连接（inner join）：返回两个表中连接字段匹配的行。如果一个表中的行在另一个表中没有匹配的行，则这些行不会出现在查询结果中。

假设有两个表，Employees 和 Departments。

SELECT Employees.Name, Departments.DeptName
FROM Employees
INNER JOIN Departments ON Employees.DeptID = Departments.DeptID;
这个查询将返回所有员工及其所在部门的信息，但仅限于那些在 Departments 表中有对应部门的员工。

②、外连接（outer join）：不仅返回两个表中匹配的行，还返回左表、右表或两者中未匹配的行。

SELECT Employees.Name, Departments.DeptName
FROM Employees
LEFT OUTER JOIN Departments ON Employees.DeptID = Departments.DeptID;
这个查询将返回所有员工的名字和他们部门的名字，即使某些员工没有分配到部门。

③、交叉连接（cross join）：返回第一个表中的每一行与第二个表中的每一行的组合，这种类型的连接通常用于生成笛卡尔积。

SELECT Employees.Name, Departments.DeptName
FROM Employees
CROSS JOIN Departments;
这个查询将为 Employees 表中的每个员工与 Departments 表中的每个部门生成一个组合。

④、笛卡尔积：数学中的一个概念，例如集合 A={a,b}，集合 B={0,1,2}，那么 A✖️B={<a,0>,<a,1>,<a,2>,<b,0>,<b,1>,<b,2>,}。
