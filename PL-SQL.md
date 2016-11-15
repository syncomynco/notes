
# **PL/SQL**

## SQL Command Categories

SQL commands are grouped into four major categories depending on their functionality. They are as follows:

### Data Definition Language (DDL)

These SQL commands are used for creating, modifying, and dropping the structure of database objects. The commands are CREATE, ALTER, DROP, RENAME, and TRUNCATE.

### Data Manipulation Language (DML)

These SQL commands are used for storing, retrieving, modifying, and deleting data. These commands are SELECT, INSERT, UPDATE, and DELETE.

### Transaction Control Language (TCL)

These SQL commands are used for managing changes affecting the data. These commands are COMMIT, ROLLBACK, and SAVEPOINT.

### Data Control Language (DCL)

These SQL commands are used for providing security to database objects. These commands are GRANT and REVOKE.

...
## PL/SQL Variables

### General Syntax to declare a variable is

```pl/sql
variable_name datatype [NOT NULL := value ];

-- Example :

DECLARE
salary number(4);
dept varchar2(10) NOT NULL := “HR Dept”;

```

The value of a variable can change in the execution or exception section of the PL/SQL Block. We can assign values to variables in the two ways given below.

1. We can directly assign values to variables.

```pl/sql
variable_name:=  value;

```
2. We can assign values to variables directly from the database columns by using a SELECT.. INTO statement. The General Syntax is:

```pl/sql
SELECT column_name
INTO variable_name 
FROM table_name 
[WHERE condition]; 
```
### Scope of PS/SQL Variables

- **Local variables** : These are declared in a inner block and cannot be referenced by outside Blocks.
- **Global Variables** : These are declared in a outer block and can be referenced by its itself and by its inner blocks.

---

## PL/SQL Constants

### General Syntax to Declare A Constan Is :

```
constant_name CONSTANT datatype := VALUE;
```
---

## PL/SQL Records

### What are records?

Records are another type of datatypes which oracle allows to be defined as a placeholder. Records are composite datatypes, which means it is a combination of different scalar datatypes like char, varchar, number etc.  Each scalar data types in the record holds a value. A record can be visualized as a row of data. It can contain all the contents of a row. 

### Declaring a record:
To declare a record, you must first define a composite datatype; then declare a record for that type.

### The General Syntax to define a composite datatype is:
```
TYPE record_type_name IS RECORD
(first_col_name column_datatype,
second_col_name column_datatype, ...);
```

- *record_type_name* : it is the name of the composite type you want to define.

- *first_col_name, second_col_name, etc.,* : these are the names of fields/columns within the record.

- *column_datatype* : defines the scalar datatype of the fields.

There are different ways you can declare the datatype of the fields. 

1. You can declare the field in the same way as you declare the fieds while creating the table. 
2. If a field is based on a column from database table, you can define the *field_type* as follows:
```
col_name table_name.column_name%type;
```
**_NOTE_** : You can use also _%type_ to declare variables and constants.
The General Syntax to declare a record of a user-defined datatype is:
```
record_name record_type_name; 
```
The following code shows how to declare a record called employee_rec based on a user-defined type.

```
DECLARE 
TYPE employee_type IS RECORD 
(employee_id number(5), 
 employee_first_name varchar2(25), 
 employee_last_name employee.last_name%type, 
 employee_dept employee.dept%type); 
 employee_salary employee.salary%type;
 employee_rec employee_type;
```

If all the fields of a record are based on the columns of a table, we can declare the record as follows:

```
record_name table_name%ROWTYPE;

```
_**NOTE**_ : When you are creating a record, you are just creating a datatype, similar to creating a variable. You need to assign values to the record to use them.

|Syntax | Usage|
|-------|------|
|TYPE record_type_name IS RECORD (column_name1 datatype, column_name2 datatype, ...);|Define a composite datatype, where each field is scalar|
|col_name table_name.column_name%type;|Dynamically define the datatype of a column based on a database column.|
|record_name record_type_name;|Declare a record based on a user-defined type.|
|record_name table_name%ROWTYPE;|Dynamically declare a record based on an entire row of a table. Each column in the table corresponds to a field in the record.|

### Examples

The following example demonstrates using the SELECT INTO statement to query a single value into a PL/SQL variable, entire columns into PL/SQL collections, or entire rows into a PL/SQL collection of records:
```
DECLARE
   howmany NUMBER;
   some_first employees.first_name%TYPE;
   some_last employees.last_name%TYPE;
   some_employee employees%ROWTYPE;
   TYPE first_typ IS TABLE OF employees.first_name%TYPE INDEX BY PLS_INTEGER;
   TYPE last_typ IS TABLE OF employees.first_name%TYPE INDEX BY PLS_INTEGER;
   first_names first_typ;
   last_names last_typ;
   CURSOR c1 IS SELECT first_name, last_name FROM employees;
   TYPE name_typ IS TABLE OF c1%ROWTYPE INDEX BY PLS_INTEGER;
   all_names name_typ;
   TYPE emp_typ IS TABLE OF employees%ROWTYPE INDEX BY PLS_INTEGER;
   all_employees emp_typ;
BEGIN
-- Query a single value and store it in a variable.
   SELECT COUNT(*) INTO howmany FROM user_tables;
   dbms_output.put_line('This schema owns ' || howmany || ' tables.');

-- Query multiple columns from one row, and store them in variables.
   SELECT first_name, last_name INTO some_first, some_last
      FROM employees WHERE ROWNUM < 2;
   dbms_output.put_line('Random employee: ' || some_first ||
      ' ' || some_last);

-- Query a single row and store it in a record.
   SELECT * INTO some_employee FROM employees WHERE ROWNUM < 2;

-- Query multiple columns from multiple rows, and store them in a collection
-- of records.
   SELECT first_name, last_name BULK COLLECT INTO all_names FROM EMPLOYEES;

-- Query multiple columns from multiple rows, and store them in separate
-- collections. (Generally less useful than a single collection of records.)
   SELECT first_name, last_name
      BULK COLLECT INTO first_names, last_names
      FROM EMPLOYEES;

-- Query an entire (small!) table and store the rows
-- in a collection of records. Now you can manipulate the data
-- in-memory without any more I/O.
   SELECT * BULK COLLECT INTO all_employees FROM employees;
END;
```
| Syntax | Usage |  
| ------- |-------- |
|record_name.col_name := value;| To directly assign a value to a specific column of a record.
|record_name.column_name := value; | To directly assign a value to a specific column of a record, if the record is declared using %ROWTYPE.|
|SELECT col1, col2 INTO record_name.col_name1, record_name.col_name2 FROM table_name [WHERE clause];| To assign values to each field of a record from the database table.| SELECT * INTO record_name FROM table_name [WHERE clause];|To assign a value to all fields in the record from a database table.| variable_name := record_name.col_name;| To get a value from a record column and assigning it to a variable.|


---

## PL/SQL Conditional Statements

As the name implies, PL/SQL supports programming language features like conditional statements, iterative statements.

The programming constructs are similar to how you use in programming languages like Java and C++
```
IF condition 1 
THEN 
 statement 1; 
 statement 2; 
ELSIF condtion2 THEN 
 statement 3; 
ELSE 
 statement 4; 
END IF
```

## Iterative Statements in PL/SQL
There are three types of loops in PL/SQL:

- Simple Loop :
A Simple Loop is used when a set of statements is to be executed at least once before the loop terminates. An EXIT condition must be specified in the loop, otherwise the loop will get into an infinite number of iterations. When the EXIT condition is satisfied the process exits from the loop.

General Syntax to write a Simple Loop is :  
```
:
LOOP 
   statements; 
   EXIT; 
   {or EXIT WHEN condition;}
END LOOP;
```

- While Loop : 
	A WHILE LOOP is used when a set of statements has to be executed as long as a condition is true. The condition is evaluated at the beginning of each iteration. The iteration continues until the condition becomes false.
	
General Syntax to write a WHILE LOOP is:
```
WHILE <condition> 
 LOOP statements; 
END LOOP; 
```
- For Loop :
A FOR LOOP is used to execute a set of statements for a predetermined number of times. Iteration occurs between the start and end integer values given. The counter is always incremented by 1. The loop exits when the counter reachs the value of the end integer.

General Syntax to write a WHILE LOOP is:
```
-- val1 := Start integer value.
-- val2 := End integer value.

FOR counter IN val1..val2 
  LOOP statements; 
END LOOP;
```
---
## PL/SQL Cursors
	