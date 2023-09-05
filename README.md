# SQL-test

1. Design databse schema for this structure.

![image](https://github.com/adityadeole24/SQL-test/assets/78893669/9f8a0f0d-7b57-46de-a882-121b14fea4e2)


2. Create an SQL query to calculate score of every employee in the department.

```
WITH EmployeeScores AS (
    -- Calculate scores for Agents
    SELECT
        e.employee_id,
        e.first_name,
        e.last_name,
        e.role,
        AVG(pc.score) AS avg_score
    FROM
        Employee e
    LEFT JOIN PhoneCall pc ON e.employee_id = pc.employee_id
    WHERE
        e.role = 'Agent'
    GROUP BY
        e.employee_id, e.first_name, e.last_name, e.role
    UNION ALL
    -- Calculate scores for TCs
    SELECT
        e.employee_id,
        e.first_name,
        e.last_name,
        e.role,
        (AVG(pc.score) + (
            SELECT AVG(score)
            FROM PhoneCall
            WHERE employee_id IN (
                SELECT employee_id
                FROM Employee
                WHERE tc_group_id = e.tc_group_id AND role = 'Agent'
            )
        )) / 2 AS avg_score
    FROM
        Employee e
    LEFT JOIN PhoneCall pc ON e.employee_id = pc.employee_id
    WHERE
        e.role = 'TC'
    GROUP BY
        e.employee_id, e.first_name, e.last_name, e.role
),
ManagerScores AS (
    -- Calculate scores for TMs
    SELECT
        e.employee_id,
        e.first_name,
        e.last_name,
        e.role,
        AVG(es.avg_score) AS avg_score
    FROM
        Employee e
    JOIN EmployeeScores es ON e.employee_id = es.manager_id
    WHERE
        e.role = 'TM'
    GROUP BY
        e.employee_id, e.first_name, e.last_name, e.role
)
SELECT
    employee_id,
    first_name,
    last_name,
    role,
    avg_score
FROM
    EmployeeScores
UNION ALL
SELECT
    employee_id,
    first_name,
    last_name,
    role,
    avg_score
FROM
    ManagerScores
UNION ALL
-- Calculate scores for TDs
SELECT
    e.employee_id,
    e.first_name,
    e.last_name,
    e.role,
    AVG(ms.avg_score) AS avg_score
FROM
    Employee e
JOIN ManagerScores ms ON e.employee_id = ms.manager_id
WHERE
    e.role = 'TD'
GROUP BY
    e.employee_id, e.first_name, e.last_name, e.role;

```


## Results

1. Created 3 tables Employee, PhoneCall, TCGroup

![image](https://github.com/adityadeole24/SQL-test/assets/78893669/1c7705e7-02ea-4a26-905c-9e32cfb10a36)

![image](https://github.com/adityadeole24/SQL-test/assets/78893669/ba871409-7b32-4939-a5ba-47cd7124d88c)

![image](https://github.com/adityadeole24/SQL-test/assets/78893669/36e67e2e-c891-400b-9bcf-074186757d4f)


2. Added dummy data in each table.

E.g. Added data into table Employee

![image](https://github.com/adityadeole24/SQL-test/assets/78893669/b5b3af1e-820b-4f5a-a8ed-705bc85da799)


3. Ran the above SQL query and got the below results.




