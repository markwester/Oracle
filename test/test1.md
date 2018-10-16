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
优化指导：
```
Plan hash value: 3808327043

 
---------------------------------------------------------------------------------------------------
| Id  | Operation                     | Name              | Rows  | Bytes | Cost (%CPU)| Time     |
---------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT              |                   |     1 |    23 |     5  (20)| 00:00:01 |
|   1 |  HASH GROUP BY                |                   |     1 |    23 |     5  (20)| 00:00:01 |
|   2 |   NESTED LOOPS                |                   |    19 |   437 |     4   (0)| 00:00:01 |
|   3 |    NESTED LOOPS               |                   |    20 |   437 |     4   (0)| 00:00:01 |
|*  4 |     TABLE ACCESS FULL         | DEPARTMENTS       |     2 |    32 |     3   (0)| 00:00:01 |
|*  5 |     INDEX RANGE SCAN          | EMP_DEPARTMENT_IX |    10 |       |     0   (0)| 00:00:01 |
|   6 |    TABLE ACCESS BY INDEX ROWID| EMPLOYEES         |    10 |    70 |     1   (0)| 00:00:01 |
---------------------------------------------------------------------------------------------------
 
Query Block Name / Object Alias (identified by operation id):
-------------------------------------------------------------
 
   1 - SEL$1
   4 - SEL$1 / D@SEL$1
   5 - SEL$1 / E@SEL$1
   6 - SEL$1 / E@SEL$1
 
Predicate Information (identified by operation id):
---------------------------------------------------
 
   4 - filter("D"."DEPARTMENT_NAME"='IT' OR "D"."DEPARTMENT_NAME"='Sales')
   5 - access("D"."DEPARTMENT_ID"="E"."DEPARTMENT_ID")
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


优化指导：
```
Plan hash value: 2128232041

 
----------------------------------------------------------------------------------------------
| Id  | Operation                      | Name        | Rows  | Bytes | Cost (%CPU)| Time     |
----------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT               |             |     1 |    23 |     7  (29)| 00:00:01 |
|*  1 |  FILTER                        |             |       |       |            |          |
|   2 |   HASH GROUP BY                |             |     1 |    23 |     7  (29)| 00:00:01 |
|   3 |    MERGE JOIN                  |             |   106 |  2438 |     6  (17)| 00:00:01 |
|   4 |     TABLE ACCESS BY INDEX ROWID| DEPARTMENTS |    27 |   432 |     2   (0)| 00:00:01 |
|   5 |      INDEX FULL SCAN           | DEPT_ID_PK  |    27 |       |     1   (0)| 00:00:01 |
|*  6 |     SORT JOIN                  |             |   107 |   749 |     4  (25)| 00:00:01 |
|   7 |      TABLE ACCESS FULL         | EMPLOYEES   |   107 |   749 |     3   (0)| 00:00:01 |
----------------------------------------------------------------------------------------------
 
Query Block Name / Object Alias (identified by operation id):
-------------------------------------------------------------
 
   1 - SEL$1
   4 - SEL$1 / D@SEL$1
   5 - SEL$1 / D@SEL$1
   7 - SEL$1 / E@SEL$1
 
Predicate Information (identified by operation id):
---------------------------------------------------
 
   1 - filter("DEPARTMENT_NAME"='IT' OR "DEPARTMENT_NAME"='Sales')
   6 - access("D"."DEPARTMENT_ID"="E"."DEPARTMENT_ID")
       filter("D"."DEPARTMENT_ID"="E"."DEPARTMENT_ID")
 ```

查询结果相同

序号 | DEPARTMENT_NAME  | 部门总人数  | 平均工资
---|---|---|---
1 | IT | 5 | 5760
2 | Sales | 34 | 8955.882352941176470588235294117647058824

查询结果用时，查询1>查询2

分析原因：主要原因查询1的关键字大小写不一，导致数据库系统进行查找时对sql语句进行的大小写不一处理。

使用查询2进行优化指导，经观察sqldeveloper的优化指导工具并未给出优化信息，所以此语句最优

自定义设计sql语句：
```sql
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
AND d.department_name in ('IT'，'Sales');
GROUP BY department_name
```
