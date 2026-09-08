# HomeSphere (Java Swing Edition)

![HomeSphere Banner](./assets/house2.jpg)

A desktop-based real estate platform built with **Java Swing** and **MySQL/JDBC**, supporting Buyer, Seller/Agent, Admin, and Office roles — inspired by platforms like 99acres and MagicBricks.

> **Note:** This is the original legacy version of the project. An enhanced rebuild (Python + FastAPI + Streamlit + GenAI features) is planned/in progress as a separate version.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Tools & Technologies](#tools--technologies)
- [Project Architecture](#project-architecture)
- [Workflow Implementation](#workflow-implementation)
- [Key Features](#key-features)
- [Main Functionalitues](#main-functionalitues)
- [Project Structure](#project-structure)
- [How to Run the Project](#how-to-run-the-project)
- [Results & Conclusion](#results--conclusion)
- [Future Improvements](#future-improvements)
- [Author & Contact](#author--contact)
- [Course Information](course-information)
- [Institution](#institution)

---

## Project Overview

HomeSphere is a Java Swing desktop application that simulates a real estate marketplace where buyers can search and express interest in properties, agents can manage listings and close deals, office staff can manage agents and view transactions, and admins can run direct database queries. The project was built as a DBMS (Database Management Systems) course project to demonstrate relational schema design, multi-role authentication, and end-to-end CRUD workflows using JDBC.

---

## Problem Statement

Real estate platforms need to serve multiple stakeholders — buyers searching for properties, sellers/owners listing them, agents brokering deals, and administrators overseeing the platform — each with different data access needs and workflows. This project aims to model that multi-role ecosystem end-to-end:

- Buyers need to search/filter properties and express interest.
- Agents need to track buyer interest and finalize sales/rentals.
- Office staff need to manage the agent workforce and monitor all transactions.
- Admins need direct oversight of the underlying data.

The project solves this by designing a relational schema connecting owners, properties, agents, buyers, deals, and transactions, and building a role-based desktop interface on top of it.

---

## Dataset

No external dataset was used in this version — all data (owners, properties, agents, buyers, transactions) was **manually/AI-generated dummy data** inserted directly into the MySQL database for testing and demonstration purposes.

> A later iteration of this project plans to seed the database with real housing data (e.g. Kaggle Indian housing datasets) instead of synthetic data.

---

## Tools & Technologies

| Category     | Technology                        |
|--------------|-----------------------------------|
| Language     | Java                              |
| GUI Framework| Java Swing / AWT                  |
| Database     | MySQL                             |
| Connectivity | JDBC (`com.mysql.cj.jdbc.Driver`) |
| IDE          | IntelliJ IDEA                     |
| Diagram      | draw.io (ER diagram)              |

---

## Project Architecture

The application follows a simple two-tier architecture: the Swing GUI layer talks directly to MySQL via JDBC, with no intermediate service/API layer.

```
┌─────────────────────────────┐
│   Java Swing GUI Layer      │   (one JFrame class per screen)
│  Login screens, dashboards, |
│  search/filter, forms       │
└────────────┬────────────────┘
             │ JDBC (Connection / Statement / PreparedStatement)
             ▼
┌─────────────────────────────┐
│      MySQL Database         │
│  owner, property, Agent,    │
│  Buyer, Owns, Manages,      │
│  Deals, Contact,            │
│  transaction, Deleted_Agent │
└─────────────────────────────┘
```

Navigation between screens is handled by each `JFrame` class invoking another screen's `main(String[] args)` method directly, passing IDs/credentials as string arguments — acting as a lightweight, if unconventional, substitute for a router/session object.

---

## Workflow Implementation

1. **Landing Page (`mypro.java`)** — entry point with four login options: Admin, Agent, Buyer, Office, plus a public "Search Properties" and "Contact Us" flow.
2. **Authentication**
   - Admin & Office: hardcoded credential check.
   - Agent & Buyer: DB-backed authentication via parameterized queries against `Agent`/`Buyer` tables.
3. **Buyer Flow**: `Filter` → `FilterQuery` (search by area/BHK/price) → select a property → `BuyerSingup` (signup + express interest, writes to `Deals`) → `BuyerSuccess` dashboard → `PurchasedProp` (view completed purchases).
4. **Agent Flow**: `AgentSuccess` dashboard → `HoldedProp` (properties managed) → `RequestProp` (view buyer interest, finalize price/date) → writes to `transaction`, updates property `status` to Sold.
5. **Office Flow**: `OfficeSuccess` dashboard → `ShowAllAgent`, `AddAgent`, `DeleteAgent` (with property/contact reassignment before deletion), `AllTransactions`.
6. **Admin Flow**: `RunQuery` → `ExecuteQuery` — a free-text SQL console executed directly against the database.

---

## Key Features

- Four distinct role-based login flows (Admin, Agent, Buyer, Office).
- Property search/filter by area, BHK type, and price range.
- Buyer "express interest" workflow tracked separately from finalized sales (`Deals` vs `transaction`).
- Agent dashboard to manage properties and convert buyer interest into closed deals.
- Agent management (add/remove) with automatic reassignment of properties and owner contacts before deletion, preserving referential integrity.
- Full transaction history view for office/admin oversight.
- Direct SQL query console for admin-level database access.
- Contact Us and Contact Agent forms for direct communication.

---

## Main Functionalitues

1. Property Search and Filtering
- Advanced search functionality based on location, property type, and price range.
- User-friendly browsing of search results with detailed property descriptions.

2. User Account Management
- Secure login system with roles for buyers, agents, and administrators.
- Role-based access control to ensure users have access to relevant information and actions.

3. Property and Agent Information
- Detailed property listings including images, dimensions, types, and status.
- Easy access to agent contact information for seamless communication.

4. Transaction Management
- Comprehensive record-keeping of property transactions for agents and buyers.
- Status updates and history tracking for all property listings.

---

## Project Structure

```
HomeSphere/
├── src/
│   ├── mypro.java              # entry point — landing page
│   ├── AdminLogin.java / AgentLogin.java / BuyerLogin.java / OfficeLogin.java
│   ├── RunQuery.java / ExecuteQuery.java        # admin SQL console
│   ├── OfficeSuccess.java / ShowAllAgent.java / AddAgent.java / DeleteAgent.java / AllTransactions.java
│   ├── AgentSuccess.java / HoldedProp.java / RequestProp.java / ContactAgent.java
│   ├── BuyerSuccess.java / BuyerSingup.java / PurchasedProp.java
│   ├── Filter.java / FilterQuery.java           # search
│   └── ContactUs.java
├── assets/                      # logo.jpg, admin.png, image2.jpg, etc.
├── db/
│   ├── Tables.txt                # schema (CREATE TABLE statements)
│   └── Queries.txt               # reference SQL queries
└── docs/
│    └── ER1-2.drawio              # entity-relationship diagram
└── README.md
```

---

## How to Run the Project

### Prerequisites
- JDK 8+
- MySQL Server running locally
- MySQL Connector/J (JDBC driver) on the classpath

### Steps

1. **Set up the database**
   ```sql
   CREATE DATABASE project;
   USE project;
   -- run the CREATE TABLE statements from db/Tables.txt
   ```

2. **Update DB credentials** (if different from defaults) in each `.java` file's `DriverManager.getConnection(...)` call — username/password are currently hardcoded per file.

3. **Compile** (run from the project root, so relative image paths resolve correctly)
   ```bash
   javac -cp .:mysql-connector-j-x.x.x.jar -d out src/*.java
   ```

4. **Run**
   ```bash
   java -cp out:mysql-connector-j-x.x.x.jar mypro
   ```
   > If using IntelliJ, set the run configuration's **working directory** to the project root (not `src/`) so `assets/` image paths resolve correctly.

5. **Login credentials for testing**
   - Admin: `admin` / `admin123`
   - Office: `admin` / `admin123`
   - Agent/Buyer: use credentials inserted into the `Agent`/`Buyer` tables.

---

## Results & Conclusion

The project successfully demonstrates a complete relational schema and multi-role CRUD workflow for a real estate platform: buyers can discover and express interest in properties, agents can convert that interest into finalized transactions, and office/admin roles can oversee agents and transaction history. It served as a strong foundation for understanding JDBC-based database connectivity, relational integrity handling (e.g. reassignment logic on agent deletion), and role-based application design — while also surfacing clear areas for improvement around security (plaintext passwords, unparameterized queries in several modules) and architecture (tight coupling between UI and data access, GUI-based navigation instead of a proper controller layer).

---

## Future Improvements

- Migrate to a proper client-server architecture (REST API backend + web/desktop frontend) instead of direct Swing-to-DB calls.
- Hash passwords (bcrypt/argon2) instead of storing them in plaintext.
- Replace string-concatenated SQL (in filtering, agent management) with parameterized queries throughout.
- Replace the raw admin SQL console with a safer, validated query interface.
- Seed the database with real housing data instead of synthetic/dummy data.
- Add GenAI-powered features: natural-language property search, AI-assisted listing descriptions (with human-in-the-loop approval), comparable-sales-based price suggestions, and per-listing Q&A.
- Separate rent and sale listings into distinct data models to reflect their different real-world attributes.

> See the companion enhanced version of this project (Python/FastAPI/Streamlit + GenAI) for the implementation of these improvements.

---

## Author & Contact

- **[Akash Singh]**
- 📧 [akash011@gmail.com]
- 🔗 [GitHub: https://github.com/akashsingh011]

## Course Information
- **Course Name**: Database Management System (CS241) 
- **Instructor**: Dr. Sumit Mishra (Assistant Professor)

## Institution
- **Institution Name**: Indian Institute of Information Technology Guwahati
- **Year**: 2nd Year, 4th Semester