# Enforcing Schema Level Access in a Company Database

**Task: Enforcing Schema-Level Access in a Company Database**

**Scenario**

You are the database administrator of a system that contains two main departments: 

• HR (Human Resources) 

• Sales 

Your job is to restrict access so that each department only views and works with its own data. 

**Objective**

1. Create SQL logins and map them to users inside the database. 

```sql
-- Step 1: Create the database
CREATE DATABASE CompanyDB;

-- Use the new database
USE CompanyDB


-- Create login and user for HR department
CREATE LOGIN hr_login WITH PASSWORD = 'Hr@12345';
CREATE USER hr_user FOR LOGIN hr_login;

-- Create login and user for Sales department
CREATE LOGIN sales_login WITH PASSWORD = 'Sales@12345';
CREATE USER sales_user FOR LOGIN sales_login;
```

![Create Database](image/CreateCompanyDB.png)
![Create Logins and Users](image/CreateSQLlogins.png)
![Logins Users](image/LoginsScreenshot.png)


2. Create two schemas: HR and Sales. 


```sql

-- Create HR schema
CREATE SCHEMA HR;


-- Create Sales schema
CREATE SCHEMA Sales;

```
![Create Schemas](image/CreateHRAndSalesSchemas.png)

3. Create a few sample tables inside each schema. 

```sql
-- HR.Employees table
CREATE TABLE HR.Employees (
    EmployeeID INT PRIMARY KEY,
    FullName NVARCHAR(100),
    Position NVARCHAR(50),
    HireDate DATE,
    Salary DECIMAL(10, 2)
);

-- HR.Departments table
CREATE TABLE HR.Departments (
    DepartmentID INT PRIMARY KEY,
    DepartmentName NVARCHAR(100),
    ManagerID INT
);

-- Sales.Customers table
CREATE TABLE Sales.Customers (
    CustomerID INT PRIMARY KEY,
    CustomerName NVARCHAR(100),
    Email NVARCHAR(100),
    Region NVARCHAR(50)
);

-- Sales.Orders table
CREATE TABLE Sales.Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    OrderDate DATE,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (CustomerID) REFERENCES Sales.Customers(CustomerID)
);
```
![Create Tables](image/CreateTableInsideSchema.png)


4. Assign schema-level permissions so: 

 - HR users cannot access Sales data.

    ```sql
    -- Give HR user full access to HR schema
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::HR TO hr_user;
    -- Prevent HR user from accessing Sales schema
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Sales TO hr_user;

    ```
![Assign HR Permissions](image/HR_Permissions.png)
	
- Sales users cannot access HR data. 
- 
    ```sql
    -- Give Sales user full access to Sales schema
    GRANT SELECT, INSERT, UPDATE, DELETE ON SCHEMA::Sales TO sales_user;
    -- Prevent Sales user from accessing HR schema
    DENY SELECT, INSERT, UPDATE, DELETE ON SCHEMA::HR TO sales_user;
    ```
![Assign Sales Permissions](image/Sales_Permissions.png)

**Task Output Checklist**

   - Try to:
1. Connect as hr_login and access HR.Employees ( should work)
![Access HR Employees](image/Hr_AccessToHRTable.png)

2. Try to access Sales.Customers ( should be denied)

![Access Sales Customers](image/Hr_CantAccessToHRTable.png)

3. Write a short explanation:
- Why schema-level security is better than table-by-table permissions
    - Schema-level security allow to manage permissions at the schema level instead of individual tables
    but the table-by-table permissions require access to each table one by one, and apply permissions to the entire schema .
- How this setup supports data segregation in real-world companies
    - This setup ensures that each department can only access its own data, preventing unauthorized access and maintaining data integrity. It allows for clear separation of concerns, which is crucial in environments where sensitive information is handled, such as HR and Sales.


------------
# Reflection Report Instructions
## Understanding SQL Security Levels and Real-World Risks

**1. What are SQL Security Levels?**

**Explain:**

   • **Server-level login** -> This is the highest level of security in SQL Server, where users are authenticated at the server level. It allows access to the SQL Server instance itself.

   • **Database-level user** -> This is a user that is created within a specific database, allowing access to that database's objects and data.
   
   • **Schema-level permissions** -> This allows for grouping of database objects and managing permissions at the schema level, which is more efficient than managing permissions on individual tables.
   
   • **Object-level permissions** -> This allows for fine-grained control over specific database objects, such as tables or views, enabling or restricting access to those objects for specific users or roles.

**2. Benefits of Applying Security Levels**

• Protect sensitive data from unauthorized access

• Ensure data integrity by preventing unauthorized modifications

• Control user access to specific data and operations

• Simplify permission management by grouping objects into schemas


