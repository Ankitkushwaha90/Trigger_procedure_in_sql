### Full Explanation of Triggers and Procedures in an SQL Database

We will work with a sample **`students` table** and a **`students_log` table** to demonstrate:

1. **What is a Trigger?**  
   - A *trigger* is a set of instructions that automatically execute in response to specific database events like `INSERT`, `UPDATE`, or `DELETE`.

2. **What is a Procedure?**  
   - A *procedure* is a stored program in the database that can be executed with a call. It allows the reuse of code.

---

## Full Example: Trigger and Procedure with Explanation

### 1. **Table Setup**
We will create two tables:
- `students`: Stores student records.
- `students_log`: Keeps a log of changes (insertions or updates) in the `students` table.

#### SQL Code to Create Tables:
```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    student_name VARCHAR(100),
    grade INT
);

CREATE TABLE students_log (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    student_id INT,
    action VARCHAR(50),
    action_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

### 2. **Trigger Example**

#### **Trigger Logic:**
We want to log whenever a student record is **INSERTED** or **UPDATED** into the `students` table. The trigger will insert data into the `students_log` table.

#### SQL Code for the Trigger:
Here, we create **two triggers**:
- **AFTER INSERT Trigger**
- **AFTER UPDATE Trigger**
```sql
DELIMITER $$ ##start from in this command line
## write content for trigger
SELECT * FROM table_name;
END $$ ##end from in this command line
```
```sql
-- Trigger for AFTER INSERT
CREATE TRIGGER after_student_insert
AFTER INSERT ON students
FOR EACH ROW
BEGIN
    INSERT INTO students_log (student_id, action)
    VALUES (NEW.student_id, 'INSERT');
END;

-- Trigger for AFTER UPDATE
CREATE TRIGGER after_student_update
AFTER UPDATE ON students
FOR EACH ROW
BEGIN
    INSERT INTO students_log (student_id, action)
    VALUES (NEW.student_id, 'UPDATE');
END;
```

#### Explanation of the Triggers:
- **`AFTER INSERT` Trigger**:  
   - Fires after a new row is inserted into the `students` table.  
   - `NEW.student_id` refers to the `student_id` of the newly inserted row.
- **`AFTER UPDATE` Trigger**:  
   - Fires after an existing row in the `students` table is updated.  
   - `NEW` contains the updated values of the row.
- **Action**: Inserts a log entry into the `students_log` table.

---

### 3. **Procedure Example**

#### **Procedure Logic:**
We will create a procedure to **add a new student** into the `students` table. This procedure takes `student_name` and `grade` as input and performs the `INSERT` operation.

#### SQL Code for the Procedure:
```sql
DELIMITER //

CREATE PROCEDURE add_student(IN name VARCHAR(100), IN grade_val INT)
BEGIN
    INSERT INTO students (student_name, grade)
    VALUES (name, grade_val);
END //

DELIMITER ;
```

#### Explanation of the Procedure:
1. **`IN` Parameters**:
   - `name`: The student's name.
   - `grade_val`: The grade of the student.
2. **Logic**:
   - Executes an `INSERT` operation into the `students` table.
3. **DELIMITER**:
   - Changes the default SQL statement terminator (`;`) to `//` so the procedure can be defined without immediate execution.

---

### 4. **Testing the Example**

#### **Step 1: Call the Procedure to Insert Data**
```sql
CALL add_student('Alice', 90);
CALL add_student('Bob', 85);
```
The `add_student` procedure inserts rows into the `students` table.

#### **Step 2: Verify the Students Table**
```sql
SELECT * FROM students;
```
**Output:**
| student_id | student_name | grade |
|------------|--------------|-------|
| 1          | Alice        | 90    |
| 2          | Bob          | 85    |

#### **Step 3: Check the Logs Table**
```sql
SELECT * FROM students_log;
```
**Output:**
| log_id | student_id | action  | action_date         |
|--------|------------|---------|---------------------|
| 1      | 1          | INSERT  | 2024-06-08 10:30:00 |
| 2      | 2          | INSERT  | 2024-06-08 10:31:00 |

#### **Step 4: Update a Record**
```sql
UPDATE students
SET grade = 95
WHERE student_id = 1;
```

#### **Step 5: Check Logs After Update**
```sql
SELECT * FROM students_log;
```
**Output:**
| log_id | student_id | action  | action_date         |
|--------|------------|---------|---------------------|
| 1      | 1          | INSERT  | 2024-06-08 10:30:00 |
| 2      | 2          | INSERT  | 2024-06-08 10:31:00 |
| 3      | 1          | UPDATE  | 2024-06-08 10:35:00 |

---

## Summary of the Example:

### **Tables:**
- `students` stores student data.
- `students_log` logs changes.

### **Procedure:**
- `add_student` is a stored procedure to insert new records into the `students` table.

### **Triggers:**
- `after_student_insert` triggers after an `INSERT` event.
- `after_student_update` triggers after an `UPDATE` event.

### **Execution:**
1. Using the procedure, we added rows to `students`.
2. The triggers automatically logged the actions into `students_log`.

This example demonstrates how **procedures** and **triggers** can work together to manage and automate tasks in a database.

Let me know if you need further clarification or an extension! ✈️

