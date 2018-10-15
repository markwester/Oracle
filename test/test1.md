首先执行示例

1.查询1
```sql
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d，hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
```


2.查询2

```sql
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT'，'Sales');
```
查询结果相同

序号 | DEPARTMENT_NAME  | 部门总人数  | 平均工资
---|---|---|---
1 | IT | 5 | 5760
2 | Sales | 34 | 8955.882352941176470588235294117647058824

查询结果用时，查询1>查询2
分析原因：主要原因查询1的关键字大小写不一，导致数据库系统进行查找时对sql语句进行的大小写不一处理。
