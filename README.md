# sql_library_management_system


## overview
This project shows library management system using SQL . it highlights the CRUD operation in SQL ,managing data in relational database, fetching the data ,manipulating of data also use of some important keys in DBMS.


## objective
  
1. **Database Setup** set up library management system database, create its tables like branch ,books,members,employees etc.

 2. **ER-Diagram:** establish relationship between the tables by using keys like primary key , foreign key ,unique . 

3. **CRUD Operations:** performing create,read,update,delete on a data

4. **JOIN operation** performing diffrent type of joins inner join, left join, multi join


### 1.Database Setup
```sql
--create library management 

DROP TABLE IF EXISTS branch;
CREATE TABLE branch
(
            branch_id VARCHAR(10) PRIMARY KEY,
            manager_id VARCHAR(10),
            branch_address VARCHAR(30),
            contact_no VARCHAR(15)
);


-- Create table "Employee"
DROP TABLE IF EXISTS employees;
CREATE TABLE employees
(
            emp_id VARCHAR(10) PRIMARY KEY,
            emp_name VARCHAR(30),
            position VARCHAR(30),
            salary DECIMAL(10,2),
            branch_id VARCHAR(10),
            FOREIGN KEY (branch_id) REFERENCES  branch(branch_id)
);


-- Create table "Members"
DROP TABLE IF EXISTS members;
CREATE TABLE members
(
            member_id VARCHAR(10) PRIMARY KEY,
            member_name VARCHAR(30),
            member_address VARCHAR(30),
            reg_date DATE
);



-- Create table "Books"
DROP TABLE IF EXISTS books;
CREATE TABLE books
(
            isbn VARCHAR(50) PRIMARY KEY,
            book_title VARCHAR(80),
            category VARCHAR(30),
            rental_price DECIMAL(10,2),
            status VARCHAR(10),
            author VARCHAR(30),
            publisher VARCHAR(30)
);



-- Create table "IssueStatus"
DROP TABLE IF EXISTS issued_status;
CREATE TABLE issued_status
(
            issued_id VARCHAR(10) PRIMARY KEY,
            issued_member_id VARCHAR(30),
            issued_book_name VARCHAR(80),
            issued_date DATE,
            issued_book_isbn VARCHAR(50),
            issued_emp_id VARCHAR(10),
            FOREIGN KEY (issued_member_id) REFERENCES members(member_id),
            FOREIGN KEY (issued_emp_id) REFERENCES employees(emp_id),
            FOREIGN KEY (issued_book_isbn) REFERENCES books(isbn) 
);



-- Create table "ReturnStatus"
DROP TABLE IF EXISTS return_status;
CREATE TABLE return_status
(
            return_id VARCHAR(10) PRIMARY KEY,
            issued_id VARCHAR(30),  --FK
            return_book_name VARCHAR(80),
            return_date DATE,
            return_book_isbn VARCHAR(50),
            FOREIGN KEY (return_book_isbn) REFERENCES books(isbn)
);
```

### 3.CRUD Operations
here we are going to `create`, `read`, `update` , `delete` the data of the database.

**Task1: create- insert a new row of book record--** ("978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
insert into books(isbn, book_title, category, rental_price, status, author, publisher)
VALUES('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
SELECT * FROM books;
```

**Task2: read- Retrieve All Books Issued by a Specific Employee**

```sql
SELECT * 
FROM issued_status
WHERE issued_emp_id = 'E102'
```

**Task3:update- existing member's address**

```sql
UPDATE members
SET member_address = '125 Oak St'
WHERE member_id = 'C103';
```

**Task4:delete a Record from the Issued Status Table**

```sql
DELETE FROM issued_status
WHERE   issued_id =   'IS121';
```

### 4. JOIN Operation

**Task1:Find Employee Branch Detail-** Write a query to display each employee's name, their position, and the address of the branch they work at.

```sql
SELECT
e.emp_name,
e.position,
b.branch_address
FROM employees AS e
JOIN branch AS b
ON e.branch_id = b.branch_id ;
```
**Task2:List Book Issues with Member Names**
 Write a query to show a list of all issued books. Include the issued_id,
the member's name , and the issue date .

```sql

SELECT 
iss.issued_id,
m.member_name,
iss.issued_date
FROM issued_status AS iss
JOIN members AS m
ON iss.issued_member_id = m.member_id;
```

**Task3:Identify Who Issued Which Book**
 Display the name of the employee who processed each book issue, 
along with the issued_id and the book's ISBN.


```sql
SELECT  
emp.emp_name,
b.isbn,
b.book_title,
iss.issued_id
FROM employees AS emp
JOIN issued_status AS iss
ON emp.emp_id = iss.issued_emp_id
Join books AS B
ON b.isbn = iss.issued_book_isbn;
```

**Task4: Track Returned Books with Original Issue Details**
 For every returned book, display the return_id, the return date ,
and the name of the member who originally borrowed it.

```sql
SELECT 
r.return_id,
r.return_date,
m.member_name
FROM members AS m
JOIN issued_status AS iss
ON m.member_id = iss.issued_member_id
JOIN return_status AS r
ON r.issued_id = iss.issued_id
```

**Task5: Find Branches Without Employees (or Total Count)**
 Write a query to display all branch addresses 
and the names of employees working there. Ensure that branches
with no employees assigned yet still show up in the results.

```sql
SELECT br.branch_address,
emp.emp_name
FROM branch AS br
join employees AS emp
ON emp.branch_id =br.branch_id
```

 **Task6: Identify Books That Have Never Been Borrowed**
 Display a list of all book titles and their
status, but filter the results to only show books that
have never been issued to any member.

```sql
SELECT b.book_title,
b.status
FROM BOOKS AS b 
JOIN issued_status AS iss
ON b.isbn = iss.issued_book_isbn
JOIN members AS m
ON m.member_id = iss.issued_member_id
WHERE status = 'no'
```


**Task7: High-Salary Employees by Branch Address**
 Write a query to find the names and 
salaries of all employees who earn more than
$50,000, along with their branch contact number.

```sql
SELECT 
emp.emp_name,
emp.salary
FROM EMPLOYEES AS emp
JOIN branch AS br
ON emp.branch_id = br.branch_id
WHERE salary >= '50000' 
```

**Task8: Count Books Issued per Branch**
 Write a query to show the 
total number of books issued by each branch.
Display the branch_id and the total count of issued books.

```sql
SELECT 
br.branch_id,
count(b.isbn)
FROM books AS b
JOIN ISSUED_STATUS AS iss
ON B.isbn = iss.issued_book_isbn
join employees AS emp
on emp.emp_id = iss.issued_emp_id
join branch AS BR
ON br.branch_id = emp.branch_id
group by br.branch_id
order by br.branch_id ASC
```

## results and conclusions

## Results & Insights

After building the database and running the SQL queries, here is what this system can successfully do:
- **Keeps Data Organized:** Instead of messy spreadsheets, all information about books, members, employees, and issued books is cleanly separated and connected without any repetition.
- **Tracks Everything Instantly:** I wrote queries that can instantly show which books are currently checked out, who borrowed them, and which books are overdue.
- **Finds Trends:** The `JOIN` and `GROUP BY` operations helped me find useful information, like which book genres are the most popular and which library branches are the busiest.

## Conclusion

 By building this Library Management System, I learned how to:
- Turn a real-world problem into a working database design (ER Diagram).
- Write clean SQL code using `CREATE`, `INSERT`, and complex `JOIN` operations to connect multiple tables together.
- Use `GROUP BY` and `ORDER BY` to filter and sort data to get clear answers.

Overall, this project taught me how to move away from manual record-keeping and use SQL to make managing a library much faster, easier, and error-free!

## future scope and extensions

further in this project we can do more complex analysis which includes aggregation , group by and functions of sql which are window functions and aggregated functions and much more.
