<details><summary>Section 1</summary>

## What is PL/SQL?

- Procedural Language/ Standard Query Language.
- It is a procedural server side programming language
- Used to bridge the gap of SQL being non-procedural.
- Case-insensitive programming language.

## Advantages of PL\SQL

- Tight integration with SQL
- Business logic can be directly implemented at database level
- High performance, High productivity
- Support object oriented programming

```pgsql
DECLARE
    --Declaration statements
BEGIN
    --Executable statements
EXCEPTION
    --Exception handling statements
END;
```

## PL/SQL Blocks

Blocks are basic programming units in PL/SQL programming language

### Anonymous Block

As this block is created without a name, this block does not create any object in the database. Thus, the scope for reusability is zero. It compiles every time you execute.

### Named Block

It creates a database object. Complied for one time and stored for reuse.

## Early vs. Late Binding

Late binding means code is compiled at execution. Early binding means code is compiled prior to execution.

## PL/SQL Data types

### Scalar

Single values with no internal components.

**Numeric:** BINARY_DOUBLE, BINARY_FLOAT, BINARY_INTEGER, DEC, DECIMAL, DOUBLE PRECISION, FLOAT, INT, INTEGER, NATURAL, NATURALN, NUMBER, NUMERIC, PLS_INTEGER, POSITIVE, POSITIVEN, REAL, SIGNTYPE, SMALLINT

**Character:** CHAR, CHARACTER, LONG, LONG RAW, NCHAR, NVARCHAR2, RAW, ROWID, STRING, UROWID, VARCHAR, VARCHAR2

**Boolean:** BOOLEAN

**Datetime:** DATE, TIMESTAMP, TIMESTAMP WITH TIMEZONE, TIMESTAMP WITH LOCAL TIMEZONE, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND

### Large Object (LOB)

Pointers to large objects that are stored separately from other data items, such as text, graphic images, video clips, and sound waveforms.

BFILE (Binary File - Points to File Outside DB), BLOB (Binary Large Object), CLOB (Character Large Object), NCLOB (National Character Large Object)

### Composite

Data items that have internal components that can be accessed individually.

RECORDS, COLLECTIONS

### Reference

Pointers to other data items.

## PLS_INTEGER and BINARY_INTEGER

### PLS_INTEGER

- PLS_INTEGER gives better performance
- Requires less storage space than INTEGER and NUMBER types
- Use when more calculations are there
- Uses machine arithmetic; while NUMBER requires additional calls to library routines.
- INTEGER require extra runtime checks

### BINARY_INTEGER

- Used for declaring signed integer variables
- Stored in binary format , which takes less space
- Calculations can run slightly faster because the values are already stored in binary format
- Uses native math libraries which does not allocate memory to store the variable until a value is assigned.

## Note:

- NULL and Boolean cannot be printed
- To turn on output:

```pgsql
SET SERVEROUTPUT ON;
```

- Only one LONG column can be used per table.
- The minimum column width that can be specified for a varchar2 data type column is one.
- The value for a CHAR data type column is blank-padded to the maximum defined column width.

## RAW Datatype

In Oracle PL/SQL, RAW is a data type used to store binary data, or data which is byte oriented (for example, graphics or audio files). One of the most important things to note about RAW data is that it can only be queried or inserted; RAW data cannot be manipulated. RAW data is always returned as a hexadecimal character value

## CONSTANT, DEFAULT, NOT NULL

```pgsql
SET SERVEROUTPUT ON;
DECLARE
    V_PI CONSTANT NUMBER(7,6):=3.14; --Assigning is mandatory
    V_NAME VARCHAR2(20) DEFAULT 'Unknown'; --Assigning is mandatory
    V_AGE NUMBER NOT NULL :=50; --Assigning is mandatory
BEGIN
    DBMS_OUTPUT.PUT_LINE('v_pi:' ||V_PI);
    DBMS_OUTPUT.PUT_LINE('v_name:'||V_NAME);
    DBMS_OUTPUT.PUT_LINE('v_age:'||V_AGE);
END;
```

## Host/Bind/Session Variable

It is a variable of the interface. This variable can be bonded with SQL or PL\SQL anonymous block. The scope of these variables is till the end of the session. These variables always preceded with a colon (:).

```pgsql
VARIABLE v_bind1 VARCHAR2(25); --Not PL/SQL commands, SQL*Plus commands
DECLARE
BEGIN
    :v_bind1 := 'Binding 1';
    DBMS_OUTPUT.PUT_LINE(:v_bind1);
END;
--Not PL/SQL statements--
VARIABLE v_bind2 VARCHAR2(25);
VARIABLE v_bind3 VARCHAR2(25);
EXECUTE :v_bind2 := 'Binding 2'; -- SQL*Plus command
EXECUTE :v_bind3 := 'Binding 3';
PRINT :v_bind2;
PRINT; -- Displays all bind variable values in the session
----
SET AUTOPRINT ON -- To turn on automatic printing of bind variable while assigning
```

## Anchored Data type/ Inheriting data type

It is used to pick up data type and size from a previously declared object into a new variable. Advantage of this is, when you change the data type or size of the field in the table, it will also affect this variable. So there is less maintenance.

```pgsql
Ex: VNAME EMP.ENAME%TYPE;
    VNAME EMP%ROWTYPE; --> Record datatype variable
```

## Composite Variable

It is a variable created by combining two or more individual variables, called indicators, into a single variable. Records and Collections

- Records and Collections are composite variable types.
- A record is a group of related data items which can have different data types called fields. Each element is addressed by VARIABLE_NAME.FIELD_NAME.
- A collection is group of elements, which have the same data type called elements. Each element is addressed by a unique subscript. Ex: VARIABLE_NAME(INDEX).

### Table-based Record

```pgsql
DECLARE
    emp_rec emp%ROWTYPE;
BEGIN
    SELECT * INTO emp_rec FROM emp
    WHERE empno=7369;
END;
```

### Cursor-based Record

```pgsql
DECLARE
    CURSOR emp_cur IS
        SELECT empno, ename
        FROM emp;
BEGIN
    FOR i IN emp_cur LOOP
        DBMS_OUTPUT.PUT_LINE(i.ename);
    END LOOP;
END;
```

### User-defined Record

```pgsql
DECLARE
    TYPE mytype IS RECORD(
        vemp_name VARCHAR2(20),
        vemp_salary emp.sal %TYPE);
    vdata mytype;
BEGIN
    SELECT ename,sal INTO vdata FROM emp WHERE empno=7369;
    DBMS_OUTPUT.PUT_LINE(vdata.vemp_name);
END;
```

## Nested Tables

- Persistent collection - Stores data and structure physically into the database as database object
- No upper limits on rows (Unbounded)
- Need external table for its storage (STORE AS clause --while creating table)
- Initialization needed before assigning values to elements

### Nested Table type as block member

```pgsql
DECLARE
    TYPE names_table IS TABLE OF VARCHAR2(10);
    TYPE grades IS TABLE OF INTEGER(2);
    names names_table;
    marks grades;
    total INTEGER(3);
BEGIN
    names := names_table('Kavita','Pritam','Ayan','Rishav','Aziz'); -- Initialization
    names(1) := 'Gaurav'; --Assigning
    marks := grades(98,97,78,87,92);
    total := names.COUNT;
    FOR i IN 1 .. total LOOP
        DBMS_OUTPUT.PUT_LINE('Student:' || names(i) || ' Marks:' || marks(i));
    END LOOP;
END;
```

### Nested table type as Database Object

```pgsql
CREATE OR REPLACE TYPE my_nested_table IS TABLE OF VARCHAR2(10);
----
CREATE TABLE my_subject(
    sub_id NUMBER(3),
    sub_name VARCHAR2(20),
    sub_schedule_day my_nested_table --nested table type
) NESTED TABLE sub_schedule_day --name of the column you want to use as nested table column
STORE AS nested_tab_space; -- storage table for your nested table type (user-defined name)
----
INSERT INTO my_subject VALUES(101,'Maths',my_nested_table('Monday','Friday'));
----
SELECT sub.sub_id, sub.sub_name,ss_day.COLUMN_VALUE FROM my_subject sub,
    TABLE(sub.sub_schedule_day) ss_day -- Table expression
```

### Nested table using user defined datatype

```pgsql
CREATE OR REPLACE TYPE object_type AS OBJECT( --type object_type now can be used as any
                                              -- other built-in datatype like VARCHAR or NUMBER
    obj_id NUMBER,
    obj_name VARCHAR2(10) );
----
CREATE OR REPLACE TYPE my_nesd_tbl IS TABLE OF object_type;
--It is not possible to add size limit to user defined datatype like object_type(5) as we do with VARCHAR2(5)
--because it is not a scalar datatype
----
CREATE TABLE base_table(
    tab_id NUMBER,
    tab_ele my_nesd_tbl
) NESTED TABLE tab_ele STORE AS store_tab_1;
```

## VARRAYs

- Persistent collection - Stores data and structure physically into the database as database object
- Can hold fixed number of elements(Bounded)
- Modified version of Nested tables
- Stored in-line with their parent record as raw value in the parent table (No need of STORE AS clause)
- Initialization needed before assigning values to elements

### VARRAYs as block member

```pgsql
DECLARE
    TYPE team_four IS VARRAY(4) OF VARCHAR2(15);
    team team_four;
BEGIN
    team := team_four('John','Mary','Alberto','Juanita'); -- Initialization
    team(3) := 'Pierre'; --Assigning
    team(4) := 'Yvonne';
    FOR i IN 1..team.LIMIT LOOP
        DBMS_OUTPUT.PUT_LINE(i || team(i));
    END LOOP;
END;
```

### To modify VARRAY size limit

```pgsql
ALTER TYPE type_name MODIFY LIMIT new_size_limit [INVALIDATE | CASCADE]
```

**INVALIDATE:** Marks all dependent TYPES and TABLES as INVALID

**CASCADE:** Cascades(propagate) the change to all dependent TYPES and TABLES

### VARRAY as Database Object

```pgsql
CREATE OR REPLACE TYPE dbObj_vry IS VARRAY(5) OF NUMBER;
----
CREATE TABLE calendar(
    day_name VARCHAR2(25),
    day_date dbObj_vry); -- No STORE AS clause is needed
----
INSERT INTO calendar VALUES('Sunday',dbObj_vry(7,14,21,28));
----
SELECT tab1.day_name, tab1.day_date
    FROM calendar tab1; -- Without Table expression
```

| DAY_NAME | COLUMN_VALUE             |
| -------- | ------------------------ |
| Sunday   | HR.DBOBJ_VRY(7,14,21,28) |

```pgsql
SELECT tab1.day_name, vry.COLUMN_VALUE
    FROM calendar tab1,
    TABLE (tab1.day_date) vry; -- Table expression
```

| DAY_NAME | COLUMN_VALUE |
| -------- | ------------ |
| Sunday   | 7            |
| Sunday   | 14           |
| Sunday   | 21           |
| Sunday   | 28           |

## Associative Arrays/ Index by table

- Non-Persistent collection - Stores data and structure just for one session. No database object can be created.
- Unbounded collection
- Hold similar data type in key-value pair
- Can access elements using numbers and strings as subscript values.
- Similar to hash table in other languages.
- Not need of initialization before assigning values to elements

```pgsql
DECLARE
    TYPE salary IS TABLE OF NUMBER(5) INDEX BY VARCHAR2(20);
    salary_list salary;
    name VARCHAR2(20);
BEGIN
    salary_list('Ranjish') := 6200; --No initialization needed before assigning
    salary_list('Minakshi') := 75000;
    salary_list('Martin') := 10000;
    name := salary_list.FIRST;
    WHILE name IS NOT NULL LOOP
        DBMS_OUTPUT.PUT_LINE('Salary of ' || name || 'is ' || salary_list(name));
        name := salary_list.NEXT(name);
    END LOOP;
END;
```

## NLS_SORT and NLS_COMP

- In associative arrays, string index's sort order is determined by the initialization parameters NLS_SORT and NLS_COMP parameter
- If you change the value of either parameter after populating an associative array indexed by string, then the collection methods FIRST, LAST, NEXT, and PRIOR might return unexpected values or raise exceptions.
- If you have changed these parameter values during your session, restore their original values before operating on associative arrays indexed by string.
- Default value for both parameter is BINARY

## Collection Methods (3 Procedures + 7 Functions)

### DELETE

Deletes elements from collection using index.

```pgsql
names.DELETE; --delete all
names.DELETE(1); --delete index 1
names.DELETE(3,6) --delete index from 3 to 6
```

### TRIM

Deletes elements from end of varray or nested table.

```pgsql
names.TRIM; --removes one element from the end of the collection
names.TRIM(5); --removes 5 elements from the end of the collection
```

### EXTEND

Memory for storing data has to be allocated before assigning value to the individual elements in the collection. It adds elements to end of varray or nested table. Cannot be used with Associative array.

```pgsql
names.EXTEND; -- occupy one element with NULL
names.EXTEND(5); -- occupy 5 elements with NULL
names.EXTEND(5,1); --5 elements in the collection will be initialized with the value in the index 1 that is 28.
```

### EXISTS

Returns TRUE if and only if specified element (index) of varray or nested table exists.

```pgsql
IF names.EXISTS(1) THEN
    DBMS_OUTPUT.PUT_LINE(names.COUNT);
END IF;
```

### FIRST,LAST

Returns first and last index (subscript) in collection.

```pgsql
DBMS_OUTPUT.PUT_LINE (names.FIRST); -- prints the index of first element
DBMS_OUTPUT.PUT_LINE (names(names.LAST)); -- prints the value of last element
```

### COUNT

Returns number of elements in collection. No empty indexes are counted.

```pgsql
DBMS_OUTPUT.PUT_LINE(names.COUNT);
```

### LIMIT

Returns maximum number of elements that collection (varray only) can have whether it is empty or not. For nested tables and associative arrays, which have no limit in size, LIMIT will return NULL.

```pgsql
DBMS_OUTPUT.PUT_LINE(names.LIMIT);
```

### PRIOR, NEXT

Returns index that precedes and succeeds specified index.

```pgsql
DBMS_OUTPUT.PUT_LINE (names.PRIOR(3)); -- prints the index of previous element
DBMS_OUTPUT.PUT_LINE (names(names.NEXT(3))); --to print the value of next element
```

### EXTEND Procedure with 1 argument

```pgsql
DECLARE
    TYPE team_four IS VARRAY(4) OF VARCHAR2(15);
    team team_four := team_four();
BEGIN
    --if we do not want to initialize like team := team_four('John','Mary','Al','Ju');
        --EXTEND will help to initialize those memory with NULL values
    team.EXTEND(4); --will occupy 4 elements team(3) := 'Pierre';
    team(4) := 'Yvonne';
    FOR i IN 1..team.LIMIT LOOP
        DBMS_OUTPUT.PUT_LINE(i || team(i));
    END LOOP;
END;
```

### EXTEND Procedure without argument

```pgsql
DECLARE
    TYPE team_four IS VARRAY(4) OF VARCHAR2(15);
    team team_four := team_four();--have to initialize without any values though. Cannot keep it as - team team_four;
BEGIN
    FOR i IN 1..team.LIMIT LOOP
        team.EXTEND;--Only one element will be occupied with NULL. If we try to assign next element,
                    --we will get error; because the memory for the next element is not initialized.
        team(i) := 'Pierre' || i;
        DBMS_OUTPUT.PUT_LINE(i || team(i));
    END LOOP;
END;
```

</details>

<details>
    <summary>Interview Questions 1</summary>

1. Get the first day of the month

```pgsql
SELECT TRUNC (SYSDATE, 'MONTH') "First day of current month" FROM DUAL;
```

2. Get the first day of the Year

```pgsql
SELECT TRUNC (SYSDATE, 'YEAR') "Year First Day" FROM DUAL;
```

3. Get the last day of the month

```pgsql
SELECT TRUNC (LAST_DAY (SYSDATE)) "Last day of current month" FROM DUAL;
```

4. Get the last day of the year

```pgsql
SELECT ADD_MONTHS (TRUNC (SYSDATE, 'YEAR'), 12) - 1 "Year Last Day" FROM DUAL
--(TRUNC is used to get extract first day of the year from SYSDATE + 12 months = 1st Jan next year) - 1 day
```

5. Get number of days in current month

```pgsql
SELECT CAST (TO_CHAR (LAST_DAY (SYSDATE), 'dd') AS INT) number_of_days FROM DUAL;
```

6. Get number of days left in current month

```pgsql
SELECT SYSDATE,
LAST_DAY (SYSDATE) "Last",
LAST_DAY (SYSDATE) - SYSDATE "Days left" FROM DUAL;
```

7. Get number of days between two dates

```pgsql
SELECT ROUND ( (MONTHS_BETWEEN ('01-Feb-2014', '01-Mar-2012') * 30), 0) num_of_days FROM DUAL;
(OR)
SELECT TRUNC(sysdate) - TRUNC(e.hire_date) FROM employees e;
```

8. Display each months start and end date upto last month of the year

```pgsql
SELECT ADD_MONTHS (TRUNC (SYSDATE, 'MONTH'), i) start_date, TRUNC (LAST_DAY (ADD_MONTHS (SYSDATE, i))) end_date
    FROM XMLTABLE (
        'for $i in 0 to xs:int(D) return $i'
            PASSING XMLELEMENT (
                d,
                FLOOR (MONTHS_BETWEEN (ADD_MONTHS (TRUNC (SYSDATE, 'YEAR') - 1, 12), SYSDATE))
            )
    COLUMNS i INTEGER PATH '.');
```

9. Get number of seconds passed since today (since 00:00 hr)

```pgsql
SELECT (SYSDATE - TRUNC (SYSDATE)) * 24 * 60 * 60 num_of_sec_since_morning FROM DUAL;
```

10. Get number of seconds left today (till 23:59:59 hr)

```pgsql
SELECT (TRUNC (SYSDATE+1) - SYSDATE) * 24 * 60 * 60 num_of_sec_left FROM DUAL;
```

11. Check if a table exists in the current database schema

```pgsql
SELECT table_name FROM user_tables
    WHERE table_name = 'TABLE_NAME';
```

12. Check if a column exists in a table

```pgsql
SELECT column_name AS FOUND FROM user_tab_cols
    WHERE table_name = 'TABLE_NAME' AND column_name = 'COLUMN_NAME';
```

13. Showing the table structure

```pgsql
SELECT DBMS_METADATA.GET_DDL ('TABLE', 'TABLE_NAME', 'USER_NAME') FROM DUAL;
-- to get DDL for a view just replace first argument with ‘VIEW’ and second with your view name and so.
```

14. Getting current schema

```pgsql
SELECT SYS_CONTEXT ('userenv', 'current_schema') FROM DUAL;
```

15. Changing current schema

```pgsql
ALTER SESSION SET CURRENT_SCHEMA = new_schema;
```

16. Database version information

```pgsql
SELECT * FROM v$version;
```

17. Database default information

```pgsql
SELECT username, profile, default_tablespace, temporary_tablespace FROM dba_users;
```

18. Database Character Set information

```pgsql
SELECT * FROM nls_database_parameters;
```

19. Get Oracle version

```pgsql
SELECT VALUE
FROM v$system_parameter WHERE name = 'compatible';
```

20. Store data case sensitive but to index it case insensitive

```pgsql
CREATE TABLE tab (col1 VARCHAR2 (10));

CREATE INDEX idx1
ON tab (UPPER (col1));

ANALYZE TABLE tab COMPUTE STATISTICS;
--In your query you might do UPPER(..) = UPPER(..) on both sides to make it case
insensitive. Now in such cases, you might want to make your index case insensitive
so that they don’t occupy more space.
```

21. Checking autoextend on/off for Tablespaces

```pgsql
SELECT SUBSTR (file_name, 1, 50), AUTOEXTENSIBLE FROM dba_data_files;
(OR)
SELECT tablespace_name, AUTOEXTENSIBLE FROM dba_data_files;
```

22. Adding datafile to a tablespace

```pgsql
ALTER TABLESPACE data01 ADD DATAFILE '/work/oradata/STARTST/data01.dbf'SIZE 1000M AUTOEXTEND OFF;
```

</details>
