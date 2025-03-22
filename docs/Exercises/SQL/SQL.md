# <center>SQL</center>

## SQL 基础

### FROM 关键字 

`FROM` 关键字的目的主要是为了 **指定数据的来源**，这个数据来源可以有很多:

1. 单个表，比如简单的 `From student`,表示从 `student` 这个表中进行查询。
2. 多个表，比如 `From student,teacher` 表示从 `student` 和 `teacher` 这两个表中进行查询。
3. 多个表的连结，比如两个表利用 `JOIN` 进行连接，`From student JOIN teacher` 表示从 `student` 和 `teacher` 这两个表中进行查询。
   
      1. `INNER JOIN` (默认)：如果两个表中有至少一个匹配，则返回行。
      2. `OUTER JOIN`：即使只有一个表中有匹配，也返回行。
      3. `LEFT JOIN`：即使右表中没有匹配，也从左表返回所有的行。
      4. `RIGHT JOIN`：即使左表中没有匹配，也从右表返回所有的行。
      5. `FULL JOIN`：只要其中一个表中有匹配，就返回行。

4. 子查询，比如 `From (SELECT * FROM student) AS s` 表示从子查询中进行查询。
    其实也就是我们在 `FROM` 后跟的是一个查询结果集合，**本质上也是一个表，只不过是子查询得到的表**

5. 使用 `with` 子句，比如 `WITH student AS (SELECT * FROM student)` 表示从 `student` 这个表中进行查询。


    ```sql
    WITH high_salary_employees AS (
        SELECT name, salary
        FROM employees
        WHERE salary > 10000
    )
    SELECT name, salary
    FROM high_salary_employees;
    ```

    仔细看上面的代码，其实就是通过 `WITH` 子句来单独的创建一个表，然后在 `SELECT` 语句中进行查询。 **本质跟子查询是类似的**

    - `WITH` 子句具有更好的复用性，但是对于比较短小的查询，我们可以使用子查询。使用`WITH` 就有点没必要了

6. 自查询，我们经常会遇到自查询的情况，比如:

    1. 需要互相比较的情况
    2. 在表中可能不同的身份的情况...

### AS 关键字作用 **别名**
目的是为了让 **表名** / **列名称** 指定别名，增强可读性。或者换句话说就是可以让输出结果能够贴合指定的别名。

```sql
/**列的别名 */

SELECT column_name AS alias_name
FROM table_name;

/**表的别名 */
SELECT column_name(s)
FROM table_name AS alias_name;
```

**如果列名称包含空格，要求使用双引号或者方括号，避免产生歧义**

出现了以下的情况，我们会选择使用别名:

1. 使用了查询函数 `COUNT()` `SUM()` `AVG()` `MAX()` `MIN()` 等等。我们需要将结果使用 `AS` 来指定别名。
2. 使用了多个表进行联合查询，我们需要使用 `AS` 来指定别名。
3. 列很长，指定性很差
4. 把多个列结合在一起，使用 `AS` 来指定别名。

### ON 关键字 和 WHERE 关键字
> 因为这两个关键字目的都是条件过滤，所以这里一起介绍;

主要的区别就是: 

<span style="color : red">`ON` 的条件过滤是存在于 `JOIN` 语句的条件判断</span>

而 `Where` 存在于其他 `select` 语句的条件判断过滤,仅此而已.

执行顺序： `FROM` -> `ON` -> `JOIN` -> `WHERE` -> `GROUP BY` -> `HAVING` -> `SELECT` -> `ORDER BY` -> `LIMIT`

## SQL 高级用法

### SQL IN 关键字

`IN` 关键字用于在 `WHERE` 子句中规定多个值。

举例:
```sql
SELECT column1, column2, ...
FROM table_name
WHERE column IN (value1, value2, ...);
```
其实就有点判断，某个列中的字段值是否在括号中的值中，如果在，就返回结果。

#### NOT IN 
与`IN` 相反，如果不在括号中的值中，就返回结果。


具体的举例可以查看隔壁的例题 **从未购物过的客户**


## SQL 函数