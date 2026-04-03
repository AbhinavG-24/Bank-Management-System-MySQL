# 🏦 Bank Management System — MySQL DBMS Project

> **A relational database system for core banking operations built in MySQL — covering customer management, account handling, transactions, loans, and automated business logic using stored procedures, triggers, and functions.**

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Key Features](#key-features)
3. [System Requirements](#system-requirements)
4. [Database Schema](#database-schema)
5. [Table Structure](#table-structure)
6. [SQL Concepts Demonstrated](#sql-concepts-demonstrated)
7. [PL/SQL Components](#plsql-components)
8. [Algorithms & Complexities](#algorithms--complexities)
9. [Project Structure](#project-structure)
10. [How to Run](#how-to-run)
11. [Sample Queries](#sample-queries)
12. [Future Roadmap](#future-roadmap)
13. [References](#references)

---

## 🏛️ Project Overview

Banks require secure and optimized databases to maintain customer and transactional records consistently. Manual banking processes are slow, error-prone, and lack centralized data management.

The **Bank Management System** is a MySQL-based relational database project that simulates real banking operations — customer registration, account creation, deposits, withdrawals, fund transfers, and loan management. All business logic is automated at the database level using **stored procedures**, **triggers**, and **functions**, ensuring data integrity without any application-layer code.

> **Course:** Database Management Systems Lab (24B15CS213) — B.Tech III Semester

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 👤 Customer Management | Add, update, delete, and search customer profiles |
| 🏧 Account Management | Create Savings, Current, Salary, and FD accounts per customer |
| 💸 Deposit & Withdrawal | Stored procedures handle deposits and withdrawals securely |
| 🔁 Fund Transfer | Transfer funds between accounts with full ledger logging |
| 📒 Transaction Ledger | Every transaction logged with type, amount, date, time, and status |
| 🏠 Loan Management | Apply for Home, Auto, Personal, Education, and Business loans |
| 🔒 Overdraft Prevention | Trigger automatically blocks withdrawals that exceed balance |
| 💰 Balance Summary | Function computes total balance across all accounts of a customer |
| 👨‍💼 Admin Staff | Role-based admin table with Teller, Manager, Auditor roles |
| 🔗 Referential Integrity | Foreign keys enforce valid relationships across all tables |

---

## 🖥️ System Requirements

### Software Requirements

| Component | Requirement |
|---|---|
| **Database** | MySQL Server 8.0 or higher |
| **IDE** | MySQL Workbench / DBeaver / any MySQL client |
| **OS** | Windows / Linux / macOS |

### Functional Requirements

- Add / Update / Delete customer details
- Create and manage bank accounts (multiple per customer)
- Record deposits, withdrawals, and transfers
- Maintain a complete and auditable transaction ledger
- Process and track loan applications
- Automatically prevent overdrafts via trigger
- Generate customer balance summaries via function

---

## 🗄️ Database Schema

```
Customer ──< Account ──< TransactionLedger
    │
    └──< Loan

AdminStaff (independent — manages system access)
```

---

## 📐 Table Structure

### Customer
| Column | Type | Constraint |
|---|---|---|
| `customerid` | INT | PRIMARY KEY, AUTO_INCREMENT |
| `name` | VARCHAR(100) | NOT NULL |
| `dob` | DATE | NOT NULL |
| `address` | VARCHAR(255) | — |
| `phone` | VARCHAR(20) | — |
| `email` | VARCHAR(120) | UNIQUE |

### Account
| Column | Type | Constraint |
|---|---|---|
| `accountno` | BIGINT | PRIMARY KEY, AUTO_INCREMENT |
| `customerid` | INT | FOREIGN KEY → Customer |
| `accounttype` | ENUM | savings / current / salary / fd |
| `balance` | DECIMAL(15,2) | DEFAULT 0.00 |
| `datecreated` | DATE | NOT NULL |
| `status` | ENUM | active / frozen / closed |

### TransactionLedger
| Column | Type | Constraint |
|---|---|---|
| `txnid` | BIGINT | PRIMARY KEY, AUTO_INCREMENT |
| `accountno` | BIGINT | FOREIGN KEY → Account |
| `type` | ENUM | deposit / withdraw / transfer_in / transfer_out |
| `amount` | DECIMAL(15,2) | NOT NULL |
| `txndate` | DATE | NOT NULL |
| `txntime` | TIME | NOT NULL |
| `status` | ENUM | pending / success / failed |

### Loan
| Column | Type | Constraint |
|---|---|---|
| `loanid` | BIGINT | PRIMARY KEY, AUTO_INCREMENT |
| `customerid` | INT | FOREIGN KEY → Customer |
| `loantype` | ENUM | home / auto / personal / education / business |
| `amount` | DECIMAL(15,2) | NOT NULL |
| `interestrate` | DECIMAL(5,2) | NOT NULL |
| `durationmonths` | INT | NOT NULL |
| `status` | ENUM | applied / approved / active / closed / rejected |

### AdminStaff
| Column | Type | Constraint |
|---|---|---|
| `adminid` | INT | PRIMARY KEY, AUTO_INCREMENT |
| `username` | VARCHAR(50) | UNIQUE, NOT NULL |
| `passwordhash` | VARCHAR(255) | NOT NULL |
| `role` | ENUM | admin / teller / manager / auditor |
| `active` | BOOLEAN | DEFAULT true |

---

## 📚 SQL Concepts Demonstrated

| Category | Concepts Used |
|---|---|
| **DDL** | `CREATE DATABASE`, `CREATE TABLE`, `ALTER`, `DROP` |
| **DML** | `INSERT`, `UPDATE`, `DELETE` |
| **DQL** | `SELECT` with `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING` |
| **Joins** | `INNER JOIN`, `LEFT JOIN` across Customer, Account, Loan |
| **Subqueries** | Nested queries for filtering and analytics |
| **Constraints** | `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `NOT NULL`, `DEFAULT` |
| **Aggregate Functions** | `SUM()`, `COUNT()`, `AVG()`, `MAX()` |
| **Transaction Control** | `START TRANSACTION`, `COMMIT`, `ROLLBACK` |
| **Indexes** | Auto-indexed via primary and unique keys |
| **ENUM Types** | Used across account types, loan types, statuses |

---

## 🔧 PL/SQL Components

### Stored Procedure — Deposit (`sp_deposit`)
```sql
CALL sp_deposit(accountno, amount);
```
Inserts a deposit record into `TransactionLedger` and updates `Account.balance`.

### Stored Procedure — Withdraw (`sp_withdraw`)
```sql
CALL sp_withdraw(accountno, amount);
```
Checks balance, logs withdrawal, and updates `Account.balance`. Blocked by overdraft trigger if balance insufficient.

### Stored Procedure — Transfer (`sp_transfer`)
```sql
CALL sp_transfer(from_account, to_account, amount);
```
Logs `transfer_out` and `transfer_in` entries and updates both account balances atomically.

### Function — Total Balance (`fn_total_balance`)
```sql
SELECT fn_total_balance(customerid);
```
Returns the sum of balances across all active accounts of a customer.

### Trigger — Prevent Overdraft (`trg_prevent_overdraft`)
Fires **BEFORE** any withdrawal or transfer. If the transaction amount exceeds the current balance, it raises an error and aborts the operation automatically — no application code needed.

---

## ⚙️ Algorithms & Complexities

| Operation | Method | Complexity |
|---|---|---|
| Customer Lookup | Primary key index scan | O(1) |
| Account Search by Customer | Foreign key index | O(log n) |
| Transaction History | Sequential scan on ledger | O(n) |
| Total Balance (function) | Aggregate `SUM` with index | O(k) — k accounts per customer |
| Overdraft Check (trigger) | Single balance lookup | O(1) |
| Loan Filtering (JOIN) | Hash join / index join | O(n log n) |

---

## 📁 Project Structure

```
bank-management-system-mysql/
├── schema.sql          # CREATE DATABASE + all table definitions
├── data.sql            # Sample INSERT statements
├── procedures.sql      # sp_deposit, sp_withdraw, sp_transfer
├── functions.sql       # fn_total_balance
├── triggers.sql        # trg_prevent_overdraft
├── queries.sql         # All sample queries (joins, subqueries, analytics)
└── README.md
```

---

## 🚀 How to Run

### Prerequisites
- MySQL Server 8.0 or higher
- MySQL Workbench or any MySQL client

### Setup — Run scripts in this order:

```sql
-- Step 1: Create database and tables
SOURCE schema.sql;

-- Step 2: Insert sample data
SOURCE data.sql;

-- Step 3: Create stored procedures
SOURCE procedures.sql;

-- Step 4: Create functions
SOURCE functions.sql;

-- Step 5: Create triggers
SOURCE triggers.sql;

-- Step 6: Run sample queries
SOURCE queries.sql;
```

---

## 🔍 Sample Queries

```sql
-- View all customers
SELECT * FROM customer;

-- View all accounts with balances
SELECT c.name, a.accountno, a.accounttype, a.balance
FROM customer c
INNER JOIN account a ON c.customerid = a.customerid;

-- Check transaction history for an account
SELECT * FROM transactionledger WHERE accountno = 1 ORDER BY txndate DESC;

-- Get total balance of a customer
SELECT fn_total_balance(1);

-- View loan details with customer names
SELECT c.name, l.loantype, l.amount, l.status
FROM customer c
INNER JOIN loan l ON c.customerid = l.customerid;

-- Find customers with savings accounts only
SELECT c.name FROM customer c
WHERE c.customerid IN (
    SELECT customerid FROM account WHERE accounttype = 'savings'
);

-- Test overdraft prevention trigger
CALL sp_withdraw(1, 999999.00);
-- Expected: ERROR — insufficient balance

-- Deposit money
CALL sp_deposit(1, 5000.00);

-- Transfer between accounts
CALL sp_transfer(1, 2, 2000.00);
```

---

## 🔭 Future Roadmap

- [ ] **GUI Frontend** — Java / Python / PHP interface connected via JDBC or MySQL connector
- [ ] **ATM Simulation Module** — Simulate PIN-based ATM withdrawal flow
- [ ] **Loan Repayment Tracking** — EMI schedule and repayment history table
- [ ] **Role-Based Access Control** — Restrict queries based on AdminStaff role
- [ ] **Cloud Deployment** — Host on AWS RDS or PlanetScale for scalability
- [ ] **Audit Log Table** — Track who made what change and when

---

## 📚 References

1. *MySQL Official Documentation* — dev.mysql.com
2. *Database System Concepts* by Korth & Silberschatz
3. W3Schools — *SQL Tutorials*
4. GeeksforGeeks — *SQL and DBMS Concepts*

---

*Built with ❤️ · Database Management Systems Lab · B.Tech CSE Semester III*
