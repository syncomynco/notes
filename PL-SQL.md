
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

**What are Cursors?**
A cursor is a temporary work area created in the system memory when a SQL statement is executed. A cursor contains information on a select statement and the rows of data accessed by it.

This temporary work area is used to store the data retrieved from the database, and manipulate this data. A cursor can hold more than one row, but can process only one row at a time. The set of rows the cursor holds is called the active set.

There are two types of cursors in PL/SQL:

### Implicit Cursors

These are created by default when DML statements like, INSERT, UPDATE, and DELETE statements are executed. They are also created when a SELECT statement that returns just one row is executed. 

Oracle provides few attributes called as implicit cursor attributes to check the status of DML operations. The cursor attributes available are %FOUND, %NOTFOUND, %ROWCOUNT, and %ISOPEN. 


The status of the cursor for each of these attributes are defined in the below table.

| Attribute | Return Value | Usage |
| --------- | ------------ | ----- |
| %FOUND | **True**, If DML statements affect at least onme row and if SELECT...INTO statement returns at least one row. **False**, if nothing is affected.| SQL%FOUND | 
| %NOTFOUND | **True**, If DML statements affect at least onme row and if SELECT...INTO statement returns at least one row. **False**, if nothing is affected. | SQL%NOTFOUND|
| %ROWCOUNT | Returns the number of affected rows by query. | SQL%ROWCOUNT |


### Explicit Cursors

They must be created when you are executing a SELECT statement that returns more than one row. Even though the cursor stores multiple records, only one record can be processed at a time, which is called as current row. When you fetch a row the current row position moves to next row.

An explicit cursor is defined in the declaration section of the PL/SQL Block. It is created on a SELECT Statement which returns more than one row. We can provide a suitable name for the cursor.

General Syntax for creating a cursor is as given below:
```
CURSOR cursor_name IS select_statement;

-- cursor_name : A suitable name for the cursor.
-- select_statement : A select query which returns multiple rows.
```
**How to use Explicit Cursor?**
There are four steps in using an Explicit Cursor :

- DECLARE the cursor in the declaration section.
- OPEN the cursor in the Execution Section.
- FETCH the data from cursor into PL/SQL variables or records in the Execution Section.
- CLOSE the cursor in the Execution Section before you end the PL/SQL Block.

**Example Usage : **

```
declare 
rec PERSONS%rowtype;
cursor cr is select * from PERSONS where AGE > 10;
begin
if not cr%ISOPEN then
open cr;
end if; 
loop
fetch cr into rec;
dbms_output.put_line(rec.firstname || ' ' || rec.lastname || ' ' || rec.person_id);
-- numrows := numrows - 1;
exit when cr%NOTFOUND;
end loop;
close cr; 
end;    
```	

---

## Stored Procedures

A stored procedure or in simple a proc is a named PL/SQL block which performs one or more specific task. This is similar to a procedure in other programming languages.

A procedure has a header and a body. The header consists of the name of the procedure and the parameters or variables passed to the procedure. The body consists or declaration section, execution section and exception section similar to a general PL/SQL Block

**Procedures: Passing Parameters**
We can pass parameters to procedures in three ways.

1. IN-parameters
2. OUT-parameters
3. IN OUT-parameters

A procedure may or may not return any value.

```	
CREATE [OR REPLACE] PROCEDURE proc_name [list of parameters] 
IS    
   Declaration section 
BEGIN    
   Execution section 
EXCEPTION    
  Exception section 
END; 
```	

**IS** : marks the beginning of the body of the procedure and is similar to DECLARE in anonymous PL/SQL Blocks. The code between IS and BEGIN forms the Declaration section.

The syntax within the brackets [ ] indicate they are optional. By using CREATE OR REPLACE together the procedure is created if no other procedure with the same name exists or the existing procedure is replaced with the current code.

```	
declare 
rec TABLE_NAME %rowtype;
cursor cr is select * from TABLE_NAME where AGE > 10;
begin
if not cr%ISOPEN then
open cr;
end if; 
loop
fetch cr into rec;
dbms_output.put_line(rec.firstname || ' ' || rec.lastname || ' ' || rec.person_id);
-- numrows := numrows - 1;
exit when cr%NOTFOUND;
end loop;
close cr; 	
end;    
```	

To use stored procedure from SQL prompt :
```	
exec [ or execute ] procedure_name ;
```	
or to use within another procedure  simply write the name of the procedure : 
```	
procedure_name ;
```	

---

## PL/SQL Functions

A function is a named PL/SQL Block which is similar to a procedure. The major difference between a procedure and a function is, a function must always return a value, but a procedure may or may not return a value.

General Syntax to create a function is : 
```
CREATE [OR REPLACE] FUNCTION function_name [parameters] 
RETURN return_datatype;  
IS  
Declaration_section  
BEGIN  
Execution_section 
Return return_variable;  
EXCEPTION  
exception section  
Return return_variable;  
END; 
```
### How to execute a PL/SQL Function?
A function can be executed in the following ways.

1. Since a function returns a value we can assign it to a variable.
```variable_name :=  func_name;```
	If ‘employee_name’ is of datatype varchar we can store the name of the employee by assigning the return type of the function to it.

2. As a part of a SELECT statement
	```SELECT func_name FROM table;```

3. In a PL/SQL Statements like,
```dbms_output.put_line(func_name);```
This line displays the value returned by the function.


### Parameters in Procedure and Functions

In PL/SQL, we can pass parameters to procedures and functions in three ways.

1. **IN** type parameter: These types of parameters are used to send values to stored procedures and they are read-only. We can assign the value of IN type parameter to a variable or use it in a query, but we cannot change its value inside the procedure. General syntax to pass a IN parameter is :
``` CREATE [OR REPLACE] PROCEDURE procedure_name ( param_name1 IN datatype, param_name12 IN datatype ... ) ```
	
2. **OUT** type parameter: These types of parameters are used to get values from stored procedures. This is similar to a return type in functions and these are write-only parameters. We cannot pass values to OUT paramters while executing the stored procedure, but we can assign values to OUT parameter inside the stored procedure and the calling program can recieve this output value. General syntax to create an OUT parameter is
```CREATE [OR REPLACE] PROCEDURE proc_name (param_name OUT datatype) ```

3. **IN OUT** parameter: These types of parameters are used to send values and get values from stored procedures. This parameter is used if the value of the IN parameter can be changed in the calling program. 
By using IN OUT parameter we can pass values into a parameter and return a value to the calling program using the same parameter. But this is possible only if the value passed to the procedure and output value have a same datatype. This parameter is used if the value of the parameter will be changed in the procedure.
General syntax to create an _IN OUT_ parameter is :
``` CREATE [OR REPLACE] PROCEDURE proc3 (param_name IN OUT datatype) ```

**NOTE** : If a parameter is not explicitly defined a parameter type, then by default it is an _IN_ type parameter.

---

## PL/SQL Exception Handling

PL/SQL Exception message consists of three parts. 
1. Type of Exception
2. An Error Code
3. A message 

General Syntax for coding the exception section

```
 DECLARE
   Declaration section 
 BEGIN 
   Exception section 
 EXCEPTION 
 WHEN ex_name1 THEN 
    -Error handling statements 
 WHEN ex_name2 THEN 
    -Error handling statements 
 WHEN Others THEN 
   -Error handling statements 
END; 
```

When an exception is raised, Oracle searches for an appropriate exception handler in the exception section. For example in the above example, if the error raised is 'ex_name1 ', then the error is handled according to the statements under it. Since, it is not possible to determine all the possible runtime errors during testing fo the code, the 'WHEN Others' exception is used to manage the exceptions that are not explicitly handled. Only one exception can be raised in a Block and the control does not return to the Execution Section after the error is handled.

If there are nested PL/SQL blocks like this.

```
 DELCARE
   Declaration section 
   -- Inner Block Start --
    DECLARE
      Declaration section 
    BEGIN 
      Execution section 
    EXCEPTION 
      Exception section 
    END; 
    -- Inner Block End --
 EXCEPTION
   Exception section 
 END;
```
 
In the above case, if the exception is raised in the inner block it should be handled in the exception block of the inner PL/SQL block else the control moves to the Exception block of the next upper PL/SQL Block. If none of the blocks handle the exception the program ends abruptly with an error.

### Tyepes Of Exceptions 

There are three types of exception;

- Named System Exceptions
- Unnamed System exceptions
- User-defined exceptions.

#### Named System Exceptions

System exceptions are automatically raised by Oracle, when a program violates a RDBMS rule. There are some system exceptions which are raised frequently, so they are pre-defined and given a name in Oracle which are known as Named System Exceptions.

For example: _NO_DATA_FOUND_ and _ZERO_DIVIDE_ are called **Named System Exceptions**.	

#### Unnamed System Exceptions
Those system exception for which oracle does not provide a name is known as unamed system exception. These exception do not occur frequently. These Exceptions have a code and an associated message.

There are two ways to handle unnamed sysyem exceptions: 
1. By using the _WHEN OTHERS_ exception handler, or 
2. By associating the exception code to a name and using it as a named exception.

We can assign a name to unnamed system exceptions using a _Pragma_ called _EXCEPTION_INIT_. 
_EXCEPTION_INIT_ will associate a predefined Oracle error number to a user defined exception name.

General syntax to declare unnamed system exception using EXCEPTION_INIT is:

```
DECLARE 
   exception_name EXCEPTION; 
   PRAGMA 
   EXCEPTION_INIT (exception_name, Err_code); 
BEGIN 
Execution section
EXCEPTION
  WHEN exception_name THEN
     handle the exception
END; 
```

#### User-defined Excetions

Apart from sytem exceptions we can explicity define exceptions based on business rules. These are known as user-defined exceptions.

Steps to be followed to use user-defined exceptions: 

- They should be explicitly declared in the declaration section. 
- They should be explicitly raised in the Execution Section. 
- They should be handled by referencing the user-defined exception name in the exception section.
General syntax for user-defined exceptions is : 
```
DECLARE
exception_name EXCEPTION;
(...)
BEGIN
(...)
RAISE exception_name;
(...)
EXCEPTION
WHEN exception_name THEN
(... handle the exception here.)
END;
/
```

#### **RAISE_APPLICATIN_ERROR()**

_RAISE_APPLICATION_ERROR_ is a built-in procedure in Oracle which is used to display the user-defined error messages along with the error number whose range is in between -20000 and -20999.

Whenever a message is displayed using RAISE_APPLICATION_ERROR, all previous transactions which are not committed within the PL/SQL Block are rolled back automatically (i.e. change due to INSERT, UPDATE, or DELETE statements).

RAISE_APPLICATION_ERROR raises an exception but does not handle it.

RAISE_APPLICATION_ERROR is used for the following reasons, 

- to create a unique id for an user-defined exception. 
- to make the user-defined exception look like an Oracle error.

General Syntax to use this procedure is:
```
RAISE_APPLICATION_ERROR (error_number, error_message); 
```
• The Error number must be between -20000 and -20999 
• The Error_message is the message you want to display when the error occurs.

Example usage :
```
DECLARE
exception_name EXCEPTION;
(...)
BEGIN
(...)
RAISE exception_name;
(...)
EXCEPTION
WHEN exception_name THEN
raise_application_error(err_code,err_message);
END;
/
```

---

## PL/SQL Triggers

A trigger is a pl/sql block structure which is fired when a DML statements like Insert, Delete, Update is executed on a database table. A trigger is triggered automatically when an associated DML statement is executed. 

**Syntax for Creating a Trigger : **

``` CREATE [OR REPLACE ] TRIGGER trigger_name 
 {BEFORE | AFTER | INSTEAD OF } 
 {INSERT [OR] | UPDATE [OR] | DELETE} 
 [OF col_name] 
 ON table_name 
 [REFERENCING OLD AS o NEW AS n] 
 [FOR EACH ROW] 
 WHEN (condition)  
 BEGIN 
   --- sql statements  
 END;
 /  ```

- **CREATE [OR REPLACE ] TRIGGER trigger_name** - This clause creates a trigger with the given name or overwrites an existing trigger with the same name.
- **{BEFORE | AFTER | INSTEAD OF }** - This clause indicates at what time should the trigger get fired. i.e for example: before or after updating a table. _INSTEAD OF_ is used to create a trigger on a view. before and after cannot be used to create a trigger on a view.
- **{INSERT [OR] | UPDATE [OR] | DELETE}** - This clause determines the triggering event. More than one triggering events can be used together separated by OR keyword. The trigger gets fired at all the specified triggering event.
-- **[OF col_name]** - This clause is used with update triggers. This clause is used when you want to trigger an event only when a specific column is updated.
-- **CREATE [OR REPLACE ] TRIGGER trigger_name** - This clause creates a trigger with the given name or overwrites an existing trigger with the same name.
- **[ON table_name]** - This clause identifies the name of the table or view to which the trigger is associated.
- **[REFERENCING OLD AS o NEW AS n]** - This clause is used to reference the old and new values of the data being changed. By default, you reference the values as :old.column_name or :new.column_name. The reference names can also be changed from old (or new) to any other user-defined name. You cannot reference old values when inserting a record, or new values when deleting a record, because they do not exist.
- **[FOR EACH ROW]** - This clause is used to determine whether a trigger must fire when each row gets affected ( i.e. a Row Level Trigger) or just once when the entire sql statement is executed(i.e.statement level Trigger).
- **WHEN (condition)** - This clause is valid only for row level triggers. The trigger is fired only for rows that satisfy the condition specified.

#### Types of PL/SQL Triggers
There are two types of triggers based on the which level it is triggered.
1. Row level trigger - An event is triggered for each row upated, inserted or deleted. 
2. Statement level trigger - An event is triggered for each sql statement executed. 

#### PL/SQL Trigger Execution Hierarchy
The following hierarchy is followed when a trigger is fired.
1. BEFORE statement trigger fires first.
2. Next BEFORE row level trigger fires, once for each row affected. 
3. Then AFTER row level trigger fires once for each affected row. This events will alternates between BEFORE and AFTER row level triggers.
4. Finally the AFTER statement level trigger fires.

We can use the data dictionary view 'USER_TRIGGERS' to obtain information about any trigger.

The below statement shows the structure of the view 'USER_TRIGGERS'
``` DESC USER_TRIGGERS;  ```

#### CYCLIC CASCADING in a TRIGGER
This is an undesirable situation where more than one trigger enter into an infinite loop. while creating a trigger we should ensure the such a situtation does not exist.

The below example shows how Trigger's can enter into cyclic cascading.
Let's consider we have two tables 'abc' and 'xyz'. Two triggers are created.

1. The INSERT Trigger, triggerA on table 'abc' issues an UPDATE on table 'xyz'.
2. The UPDATE Trigger, triggerB on table 'xyz' issues an INSERT on table 'abc'.

In such a situation, when there is a row inserted in table 'abc', triggerA fires and will update table 'xyz'. 
When the table 'xyz' is updated, triggerB fires and will insert a row in table 'abc'. 
This cyclic situation continues and will enter into a infinite loop, which will crash the database.