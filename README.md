# CMPE 332 - QL Assignment

## Part 1 - SQL

**List the SSN, name (first and last), address and salary of all employees working in a department that has a location in "Houston".**

The query is 
```sql
SELECT `SSN`, `FName`, `LName`, `Address`, `Salary` 
FROM `employee` 
WHERE `Address` 
LIKE '%Houston%'
```

Which returns
| SSN       | FName    | LName   | Address                  |Salary |
|:---------:|:--------:|:-------:|:------------------------:|:-----:|
| 123456789 | John     | Smith   | 731 Fondren, Houston, TX | 30000 |
| 333445555 | Franklin | Wong    | 638 Voss, Houston, TX    | 40000 |
| 453453453 | Joyce    | English | 5631 Rice, Houston, TX   | 25000 |
| 888665555 | James    | Borg    | 450 Stone, Houston, TX   | 55000 |
| 987987987 | Ahmad    | Jabbar  | 980 Dallas, Houston, TX  | 25000 |

**For each project managed by the "Administration" department, list the project
number and the total number of hours worked on that project by employees of the
department.**

The query is
```sql
SELECT project.PNo, SUM(works_on.Hours) 
FROM project 
INNER JOIN works_on ON project.PNo = works_on.PNo 
WHERE project.DNo IN (
    SELECT DNo
    FROM department
    WHERE DName
    LIKE '%Administration%'
)
GROUP BY project.PNo
```

where the output is

| PNo | SUM(works\_on.Hours) |
|:---:|:-------------------:|
| 10  |               55.00 |
| 30  |               25.00 |


**List the project number, project name and number of employees working on a
project for all projects with more than 2 employees working on it.**

The query is

```sql
SELECT project.PNo, project.PName, COUNT(works_on.SSN)
FROM project
INNER JOIN works_on ON project.PNo=works_on.PNo
GROUP BY project.PNo
HAVING COUNT(works_on.SSN) > 2
```

where the output is

| PNo | PName           | COUNT(works\_on.SSN) |
|:---:|:---------------:|:-------------------:|
|  2  | ProductY        |                   3 |
| 10  | Computerization |                   3 |
| 20  | Reorganization  |                   3 |

**List the SSN, name (first and last) and salary of all employees that are managers.**

The query is

```sql
SELECT SSN, FName, LName, Salary
FROM employee
WHERE SSN IN (SELECT MGRSSN FROM department)
```

where the output is

| SSN       | FName    | LName   | Salary |
|:---------:|:--------:|:-------:|:------:|
| 333445555 | Franklin | Wong    |  40000 |
| 888665555 | James    | Borg    |  55000 |
| 987654321 | Jennifer | Wallace |  43000 |

**List the SSN, name (first and last) and salary of all employees who earn more
than the average salary for the company.**

The query is

```sql
SELECT SSN, FName, LName, Salary
FROM employee
WHERE salary > (
    SELECT AVG(Salary)
    FROM employee
)
```

Where the output is

| SSN       | FName    | LName   | Salary |
|:---------:|:--------:|:-------:|:------:|
| 333445555 | Franklin | Wong    |  40000 |
| 666884444 | Ramesh   | Narayan |  38000 |
| 888665555 | James    | Borg    |  55000 |
| 987654321 | Jennifer | Wallace |  43000 |

**For each project, give the project number and name, the number of employees
working on the project, and the average number of hours worked by employees on
that project. Order the results by project number**

The query is

```sql
SELECT project.PNo, project.PName, COUNT(works_on.SSN), AVG(works_on.Hours)
FROM project
JOIN works_on
ON project.PNo = works_on.PNo
GROUP BY project.PNo
ORDER BY project.PNo
```

where the output is

| PNo | PName           | COUNT(works\_on.SSN) | AVG(works\_on.Hours) |
|:---:|:---------------:|:-------------------:|:-------------------:|
|   1 | ProductX        |                   2 | 26.250000           |
|   2 | ProductY        |                   3 | 12.500000           |
|   3 | ProductZ        |                   2 | 25.000000           |
|  10 | Computerization |                   3 | 18.333333           |
|  20 | Reorganization  |                   3 | 8.333333            |
|  30 | Newbenefits     |                   2 | 12.500000           |


**For all employees with at least one dependent, list their SSN, name (first and last),
salary and the name of the department for which they work.**

The query is

```sql
SELECT employee.SSN, employee.FName, employee.LName, employee.Salary, department.DName
FROM employee
JOIN department
ON employee.DNo = department.DNo
WHERE employee.SSN in (
    SELECT dependent.SSN
    FROM dependent
)
```

where the output is

| SSN		| FName    | LName   | Salary | DName          |
|:---------:|:--------:|:-------:|:------:|:--------------:|
| 987654321 | Jennifer | Wallace |  43000 | Administration |
| 123456789 | John     | Smith   |  30000 | Research       |
| 333445555 | Franklin | Wong    |  40000 | Research       |
