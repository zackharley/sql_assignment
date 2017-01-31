# CMPE 332 - QL Assignment

## Table of Contents

1. [Part 1 - SQL](#part-1---sql)
   1. [Populate the DB](#populate-the-db)
   2. [Queries](#queries)
2. [Part 2 - Relational Algebra](#part-2---relational-algebra)

## Part 1 - SQL

### Populate the DB

First we want to populate the DB.

#### Department

I created the Department table using:

```sql
CREATE TABLE department(
    DName VARCHAR(15) NOT NULL,
    DNo INT NOT NULL,
    MGRSSN CHAR(11) NOT NULL,
    MgrStartDate DATE NOT NULL,
    PRIMARY KEY(DNo)
)
```

After inserting the data, using

```sql
SELECT * FROM department
```

we get a dump of its contents:

| DName | DNo | MGRSSN | MgrStartDate |
| :---: | :---: | :---: | :---: |
| Headquarters | 1 | 888665555 | 1981-06-19 |
| Administration | 4 | 987654321 | 1995-01-01 |
| Research | 5 | 333445555 | 1988-05-22 |

#### Dependent

I created the Dependent table using:

```sql
CREATE TABLE dependent(
    Dependent_name CHAR(40) NOT NULL,
    Sex CHAR(1) NOT NULL,
    BDate DATE NOT NULL,
    Relationship CHAR(10) NOT NULL,
    SSN CHAR(11) NOT NULL,
    PRIMARY KEY(Dependent_name, SSN)
)
```

After inserting the data, using

```sql
SELECT * FROM dependent
```

we get a dump of its contents:

| Dependent\_name | Sex | Bdate | Relationship | SSN |
| :---: | :---: | :---: | :---: | :---: |
| Abner | M | 1942-02-28 | SPOUSE | 987654321 |
| Alice | F | 1988-12-30 | DAUGHTER | 123456789 |
| Alice | F | 1966-04-05 | DAUGHTER | 333445555 |
| Elizabeth | F | 1967-05-05 | SPOUSE | 123456789 |
| Joy | F | 1958-05-03 | SPOUSE | 333445555 |
| Michael | M | 1988-01-04 | SON | 123456789 |
| Theodore | M | 1963-10-25 | SON | 333445555 |

#### DeptLocations

I created the DeptLocations table using:

```sql
CREATE TABLE deptlocations(
    DNo INT NOT NULL,
    DLocation VARCHAR(40) NOT NULL,
    PRIMARY KEY(DNo, DLocation)
)
```

After inserting the data, using

```sql
SELECT * FROM deptlocations
```

we get a dump of its contents:

| DNo | DLocation |
| :---: | :---: |
| 1 | Houston |
| 4 | Stafford |
| 5 | Bellaire |
| 5 | Houston |
| 5 | Sugarland |

#### Employee

I created the Employee table using:

```sql
CREATE TABLE employee(
    SSN CHAR(11) NOT NULL,
    BDate DATE NOT NULL,
    Sex CHAR(1) NOT NULL,
    Address VARCHAR(40) NOT NULL,
    Salary INT NOT NULL,
    FName CHAR(20) NOT NULL,
    Minit CHAR(1) NOT NULL,
    LName CHAR(20) NOT NULL,
    DNo INT NOT NULL,
    SuperSSN CHAR(11),
    PRIMARY KEY(SSN)
)
```

After inserting the data, using

```sql
SELECT * FROM employee
```

we get a dump of its contents:

| SSN | BDate | Sex | Address | Salary | FName | Minit | LName | DNo | SuperSSN |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 123456789 | 1965-01-09 | M | 731 Fondren, Houston, TX | 30000 | John | B | Smith | 5 | 333445555 |
| 333445555 | 1955-12-08 | M | 638 Voss, Houston, TX | 40000 | Franklin | T | Wong | 5 | 888665555 |
| 453453453 | 1972-07-31 | F | 5631 Rice, Houston, TX | 25000 | Joyce | A | English | 5 | 333445555 |
| 666884444 | 1962-09-15 | M | 975 Fire Oak, Humble, TX | 38000 | Ramesh | K | Narayan | 5 | 333445555 |
| 888665555 | 1937-11-10 | M | 450 Stone, Houston, TX | 55000 | James | E | Borg | 1 | NULL |
| 987654321 | 1941-06-20 | F | 291 Berry, Bellaire, TX | 43000 | Jennifer | S | Wallace | 4 | 888665555 |
| 987987987 | 1969-03-29 | M | 980 Dallas, Houston, TX | 25000 | Ahmad | V | Jabbar | 4 | 987654321 |
| 999887777 | 1968-07-19 | F | 3321 Castle, Spring,TX | 25000 | Alicia | J | Zelaya | 4 | 987654321 |

#### Project

I created the Project table using:

```sql
CREATE TABLE project(
    PName CHAR(20) NOT NULL,
    PNo INT NOT NULL,
    PLocation CHAR(40) NOT NULL,
    DNo INT NOT NULL,
    PRIMARY KEY(PNo)
)
```

After inserting the data, using

```sql
SELECT * FROM project
```

we get a dump of its contents:

| PName | PNo | PLocation | DNo |
| :---: | :---: | :---: | :---: |
| ProductX | 1 | Bellaire | 5 |
| ProductY | 2 | Sugarland | 5 |
| ProductZ | 3 | Houston | 5 |
| Computerization | 10 | Stafford | 4 |
| Reorganization | 20 | Houston | 1 |
| Newbenefits | 30 | Stafford | 4 |

#### Works\_On

I created the Works\_On table using:

```sql
CREATE TABLE works_on(
    SSN CHAR(11) NOT NULL,
    PNo INT NOT NULL,
    Hours DECIMAL(5,2) NOT NULL,
    PRIMARY KEY(SSN, PNo)
)
```

After inserting the data, using

```sql
SELECT * FROM works_on
```

we get a dump of its contents:

| SSN | PNo | Hours |
| :---: | :---: | :---: |
| 123456789 | 1 | 32.5 |
| 123456789 | 2 | 7.5 |
| 333445555 | 2 | 10 |
| 333445555 | 3 | 10 |
| 333445555 | 10 | 10 |
| 333445555 | 20 | 10 |
| 453453453 | 1 | 20 |
| 453453453 | 2 | 20 |
| 666884444 | 3 | 40 |

### Queries

**1. List the SSN, name \(first and last\), address and salary of all employees working in a department that has a location in "Houston".**

The query is

```sql
SELECT SSN, FName, LName, Address, Salary
FROM employee 
WHERE DNo IN (
    SELECT DNo
    FROM deptlocations 
    WHERE DLocation='Houston'
);
```

Which returns

| SSN | FName | LName | Address | Salary |
| :---: | :---: | :---: | :---: | :---: |
| 123456789 | John | Smith | 731 Fondren, Houston, TX | 30000 |
| 333445555 | Franklin | Wong | 638 Voss, Houston, TX | 40000 |
| 453453453 | Joyce | English | 5631 Rice, Houston, TX | 25000 |
| 666884444 | Ramesh | Narayan | 975 Fire Oak, Humble, TX | 38000 |
| 888665555 | James | Borg | 450 Stone, Houston, TX | 55000 |

**2. For each project managed by the "Administration" department, list the project  
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

| PNo | SUM\(works\_on.Hours\) |
| :---: | :---: |
| 10 | 55.00 |
| 30 | 25.00 |

**3. List the project number, project name and number of employees working on a  
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

| PNo | PName | COUNT\(works\_on.SSN\) |
| :---: | :---: | :---: |
| 2 | ProductY | 3 |
| 10 | Computerization | 3 |
| 20 | Reorganization | 3 |

**4. List the SSN, name \(first and last\) and salary of all employees that are managers.**

The query is

```sql
SELECT SSN, FName, LName, Salary
FROM employee
WHERE SSN IN (SELECT MGRSSN FROM department)
```

where the output is

| SSN | FName | LName | Salary |
| :---: | :---: | :---: | :---: |
| 333445555 | Franklin | Wong | 40000 |
| 888665555 | James | Borg | 55000 |
| 987654321 | Jennifer | Wallace | 43000 |

**5. List the SSN, name \(first and last\) and salary of all employees who earn more  
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

| SSN | FName | LName | Salary |
| :---: | :---: | :---: | :---: |
| 333445555 | Franklin | Wong | 40000 |
| 666884444 | Ramesh | Narayan | 38000 |
| 888665555 | James | Borg | 55000 |
| 987654321 | Jennifer | Wallace | 43000 |

**6. For each project, give the project number and name, the number of employees  
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

| PNo | PName | COUNT\(works\_on.SSN\) | AVG\(works\_on.Hours\) |
| :---: | :---: | :---: | :---: |
| 1 | ProductX | 2 | 26.250000 |
| 2 | ProductY | 3 | 12.500000 |
| 3 | ProductZ | 2 | 25.000000 |
| 10 | Computerization | 3 | 18.333333 |
| 20 | Reorganization | 3 | 8.333333 |
| 30 | Newbenefits | 3 | 18.333333 |

**7. For all employees with at least one dependent, list their SSN, name \(first and last\),  
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

| SSN | FName | LName | Salary | DName |
| :---: | :---: | :---: | :---: | :---: |
| 987654321 | Jennifer | Wallace | 43000 | Administration |
| 123456789 | John | Smith | 30000 | Research |
| 333445555 | Franklin | Wong | 40000 | Research |

## Part 2 - Relational Algebra



