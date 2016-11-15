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

```
variable_name datatype [NOT NULL := value ];

-- Example :

DECLARE
salary number(4);
dept varchar2(10) NOT NULL := “HR Dept”;

```

The value of a variable can change in the execution or exception section of the PL/SQL Block. We can assign values to variables in the two ways given below.

1. We can directly assign values to variables.

```
variable_name:=  value;

```
2. We can assign values to variables directly from the database columns by using a SELECT.. INTO statement. The General Syntax is:

```
SELECT column_name
INTO variable_name 
FROM table_name 
[WHERE condition]; 
```
### Scope of PS/SQL Variables

- **Local variables** : These are declared in a inner block and cannot be referenced by outside Blocks.
- **Global Variables** : These are declared in a outer block and can be referenced by its itself and by its inner blocks.

...

## PL/SQL Constants

### General Syntax to Declare A Constan Is :

```
constant_name CONSTANT datatype := VALUE;
```
...

## PL/SQL Records

### What are records?

Records are another type of datatypes which oracle allows to be defined as a placeholder. Records are composite datatypes, which means it is a combination of different scalar datatypes like char, varchar, number etc.  Each scalar data types in the record holds a value. A record can be visualized as a row of data. It can contain all the contents of a row. 

### Declaring a record:
To declare a record, you must first define a composite datatype; then declare a record for that type.

### The General Syntax to define a composite datatype is:

```sql
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
```sql
col_name table_name.column_name%type;
```
_*NOTE*_ : You can use also _%type_ to declare variables and constants.
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
_*NOTE*_ : When you are creating a record, you are just creating a datatype, similar to creating a variable. You need to assign values to the record to use them.

|Syntax | Usage|
|-------|------|
|TYPE record_type_name IS RECORD (column_name1 datatype, column_name2 datatype, ...);|Define a composite datatype, where each field is scalar|


