# <center>题库</center>

> 因为笔者在进行 淘天 面试的时候，被考到了 `sql` 语句，但是不会写，现在用来查漏补缺练习一下

主要分成两方面，刷题 + 另外一个知识点文档的对应例题

## SQL 基础

### AS 关键字作用 **别名**

题目链接

[形成化学键](https://leetcode.cn/problems/form-a-chemical-bond/description/)

```sql
# Write your MySQL query statement below
select 
    m.symbol AS Metal,
    n.symbol AS Nonmetal
from 
    Elements m,
    Elements n
where 
    m.type = 'Metal'
    AND n.type = 'Nonmetal'
```

我们仔细看一下这个题目，目的很简单，就是找到能形成化学键的两个元素，并且输出出来。那么我们就使用 `FROM Elements m, Elements n` 来进行两个表的联合查询，然后使用 `AS` 来指定别名。或者简单的来说就是单表的复用。


## 刷题

### 超过经理收入的员工
[题目链接](https://leetcode.cn/problems/employees-earning-more-than-their-managers/description/)

```sql
Select m.name AS Employee 
FROM Employee m, Employee n
WHERE m.managerId = n.id and m.salary > n.salary;
```

这里有两个要注意的:

1. 我们使用的是自连接，也就是我们使用了两个表，但是这两个表是同一个表，所以我们使用了两个别名来进行区分。
2. 在 `Select m.name` 中，我们需要指明是哪个表的 `name` 字段，所以我们使用了 `m.name` 来进行区分。不然会出现以下的报错:

`Column 'name' in field list is ambiguous`


### 寻找表中所有有重复出现过的电子邮箱

**淘天有道真题其实跟这个很像，输出所有具有相同名字的学生的名字**

[题目链接](https://leetcode.cn/problems/duplicate-emails/)
```sql
# Write your MySQL query statement below
With Email_Table AS(
    SELECT Email AS email
    From Person
    Group BY email
    Having Count(email) > 1
)

SELECT * from Email_Table; 
```

主要的思路就是，`Group By` 进行分组，然后使用 `Having` 来进行过滤，最后使用 `With` 来进行表的复用。

### 从不订购的客户

[题目链接](https://leetcode-cn.com/problems/customers-who-never-order/)

这题其实很简单，就是 我们通过找出一个集合(表示的是所有有过订单记录的客户)，然后我们在进行过滤，找出不在这个集合中的客户,使用`NOT IN` 即可。

```sql
Select name AS customers
from Customers
where Customers.id not in (
    select customerId from  Orders
)
```


### 部门工资最高的员工

[题目链接](https://leetcode.cn/problems/department-highest-salary/description/)

这题其实就是很典型的模板题，我们把整个题目拆开，就是分组(根据每个员工的部门)，然后在每个部门中找出工资最高的员工。

```sql
SELECT
    Department.name AS 'Department',
    Employee.name AS '

Employee',
    Salary
FROM
    Employee
        JOIN
    Department ON Employee.DepartmentId = Department.Id
WHERE
    (Employee.DepartmentId , Salary) IN
    (   SELECT
            DepartmentId, MAX(Salary)
        FROM
            Employee
        GROUP BY DepartmentId
    )
;

```

这里值得注意的是，我们需要利用 `Department` 作为列名的时候，最好使用 **单引号或者是双引号** ，避免产生歧义。