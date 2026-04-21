# Employee Management System (EMS)
> Java · JDBC · MySQL · Console-based

A fully modular, menu-driven Employee Management System built with **Core Java**, **JDBC**, and **MySQL**. Follows clean OOP principles with separated Model → DAO → Service → Main layers.

---

## Features

| Module | Capabilities |
|---|---|
| **Employee** | Add / View / Search / Update / Delete |
| **Salary** | Monthly records, Bonus & Deduction, Net Salary, History, Reports |
| **Attendance** | Check-In / Check-Out, Late detection, Overtime calculation |
| **Leave** | Apply, Approve/Reject (Admin), Balance tracking |
| **Auth** | Login system with ADMIN and USER roles |

---

## Project Structure

```
EmployeeManagementSystem/
├── pom.xml                          ← Maven build file
├── README.md
├── sql/
│   └── schema.sql                   ← Database & table creation script
└── src/main/java/com/ems/
    ├── model/                       ← Plain Java objects (POJOs)
    │   ├── Employee.java
    │   ├── Salary.java
    │   ├── Attendance.java
    │   ├── Leave.java
    │   └── User.java
    ├── dao/                         ← JDBC database access
    │   ├── EmployeeDAO.java
    │   ├── SalaryDAO.java
    │   ├── AttendanceDAO.java
    │   ├── LeaveDAO.java
    │   └── UserDAO.java
    ├── service/                     ← Business logic + console I/O
    │   ├── EmployeeService.java
    │   ├── SalaryService.java
    │   ├── AttendanceService.java
    │   └── LeaveService.java
    ├── util/
    │   ├── DBConnection.java        ← Singleton JDBC connection
    │   └── InputUtil.java           ← Validated console input helpers
    └── main/
        └── MainApp.java             ← Entry point & menus
```

---

## Prerequisites

| Tool | Version |
|---|---|
| Java JDK | 11 or higher |
| MySQL | 8.0 or higher |
| Maven | 3.6 or higher (optional — can compile manually) |

---

## Step-by-Step Setup

### 1. Clone / Download the project

```bash
git clone https://github.com/yourname/employee-management-system.git
cd employee-management-system
```

### 2. Set up the MySQL database

Open your MySQL client and run the schema script:

```bash
mysql -u root -p < sql/schema.sql
```

Or paste the contents of `sql/schema.sql` directly into MySQL Workbench / DBeaver.

This creates:
- Database `employee_management`
- Tables: `users`, `employees`, `salaries`, `attendance`, `leaves`, `leave_balance`
- Seed data: 2 users (admin/user) and 3 sample employees

### 3. Configure database credentials

Open `src/main/java/com/ems/util/DBConnection.java` and update:

```java
private static final String URL      = "jdbc:mysql://localhost:3306/employee_management?useSSL=false&serverTimezone=UTC";
private static final String USER     = "root";        // ← your MySQL username
private static final String PASSWORD = "your_password"; // ← your MySQL password
```

### 4a. Build and run with Maven (recommended)

```bash
# Build a fat JAR (includes MySQL driver)
mvn clean package

# Run
java -jar target/employee-management-system-1.0.0-jar-with-dependencies.jar
```

### 4b. Build and run manually (no Maven)

Download the MySQL JDBC driver JAR from https://dev.mysql.com/downloads/connector/j/
and place it in the project root as `mysql-connector-java.jar`.

```bash
# Compile all sources
find src -name "*.java" > sources.txt
javac -cp mysql-connector-java.jar @sources.txt -d out/

# Run
java -cp out:mysql-connector-java.jar com.ems.main.MainApp
# Windows:
java -cp out;mysql-connector-java.jar com.ems.main.MainApp
```

---

## Default Login Credentials

| Role | Username | Password |
|---|---|---|
| Admin | `admin` | `admin123` |
| User  | `user`  | `user123`  |

> **Security note:** Passwords are stored in plain text for simplicity. In production, use BCrypt or a similar hashing library.

---

## Usage Guide

### Admin Menu
```
1. Employee Management   → Add / View / Search / Update / Delete employees
2. Salary Management     → Add monthly salaries, view history, monthly report
3. Attendance Management → Check-in / Check-out, mark absent, view by range
4. Leave Management      → Review & approve/reject leaves, view balances
0. Logout
```

### User (Self-Service) Menu
```
1. View My Salary History
2. Apply for Leave
3. View My Leave History
4. View My Leave Balance
5. View My Attendance
0. Logout
```

---

## Net Salary Formula

```
Net Salary = Basic Salary + Bonus − Deduction
```
Computed as a **generated column** in MySQL so it is always consistent.

---

## Attendance Logic

| Condition | Status |
|---|---|
| Check-in at or before 09:00 | PRESENT |
| Check-in after 09:00 | LATE |
| No check-in recorded | ABSENT |
| Hours worked > 8 | Overtime = total_hours − 8 |

---

## Leave Balance (per employee per year)

| Type | Default Days |
|---|---|
| Sick Leave | 10 |
| Casual Leave | 10 |
| Earned Leave | 15 |

Balance is deducted automatically when a leave application is **approved**.

---

## Database Schema Overview

```
users          ──┐
                 │ approved_by (FK)
employees ───────┼──── salaries   (emp_id FK)
          │      │──── attendance (emp_id FK)
          │      └──── leaves     (emp_id FK)
          └────────── leave_balance (emp_id FK, 1:1)
```

---

## Extending the Project

- **GUI:** Swap the Service layer's `System.out` / `Scanner` calls for Swing/JavaFX components — the DAO and Model layers need zero changes.
- **Spring Boot:** Replace `DBConnection` with a `DataSource` bean and annotate DAOs with `@Repository`.
- **Password hashing:** Add BCrypt dependency and hash passwords in `UserDAO`.
- **Reports:** Add a `ReportService` that generates CSV or PDF salary/attendance summaries.

---

## License
MIT — free to use and modify.
