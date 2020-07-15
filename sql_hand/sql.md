### 员工薪水中位数

```sql
---id----company----salary
---1-----A---------120000
---2-----B---------130000
---3-----C---------190000
---4-----A---------140000
# Write your MySQL query statement below
SELECT A.Id,A.Company,A.Salary
FROM 
(
	SELECT 
	id,
	company,
	salary,
	@trank:=IF(@pre_company=company,@trank+1,0) AS `trank`,
	@pre_company:=company
	FROM employee,(SELECT @pre_company:= NULL,@trank:=0) AS T
	ORDER BY company,salary
) AS A
JOIN 
(
	SELECT company, FLOOR((COUNT(*)-1)/2) AS `beg`, if(COUNT(*)%2=1,0,1) AS `cnt`
	FROM employee
	GROUP BY company
) AS B
ON (A.company=B.company AND A.trank BETWEEN B.beg AND B.beg+B.cnt)
```

### 员工工资前三

```sql
SELECT
	Department.NAME AS Department,
	e1.NAME AS Employee,
	e1.Salary AS Salary 
FROM
	Employee AS e1,Department 
WHERE
	e1.DepartmentId = Department.Id 
	AND 3 > (SELECT  count( DISTINCT e2.Salary ) 
			 FROM	Employee AS e2 
			 WHERE	e1.Salary < e2.Salary 	AND e1.DepartmentId = e2.DepartmentId 	) 
ORDER BY Department.NAME,Salary DESC;


//窗口
select DepartmentId,Name,Salary
from (
   select *, 
          dense_rank() over (partition by DepartmentId
                       order by Salary desc) as ranking
   from Employee) as a
where ranking <= 3;
```

### 员工最高工资

```sql
SELECT
	Department.NAME AS Department,
	Employee.NAME AS Employee,
	Salary 
FROM
	Employee,
	Department 
WHERE
	Employee.DepartmentId = Department.Id 
	AND ( Employee.DepartmentId, Salary ) 
    IN (SELECT DepartmentId, max( Salary ) 
        FROM Employee 
        GROUP BY DepartmentId )
```



