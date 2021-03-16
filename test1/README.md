# test1

#### 姓名：覃龙

#### 学号：201810414121

#### 班级：2018级软件工程1班

- 查询1：

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT','Sales')
GROUP BY d.department_name;
```

查询结果：

![](1.png)

- `查询`2：

```sql
set autotrace on

SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
FROM hr.departments d,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY d.department_name
HAVING d.department_name in ('IT','Sales');
```

查询结果：

![](3.png)

通过分析得出查询2 是最优的 。

查询2的优化建议：

![](4.png)

-  查询工资最高的3 名员工信息 ：

  ```sql
  SELECT * FROM 
  (SELECT d.department_name ,e.salary,e.first_name,e.last_name
  FROM hr.departments d,hr.employees e 
  WHERE d.department_id = e.department_id
  ORDER BY e.salary DESC) 
  WHERE ROWNUM <= 3;
  ```

  先查询出所有的信息通过工资排序，再筛选出前3名。

![](6.png)

优化结果：

![](5.png)