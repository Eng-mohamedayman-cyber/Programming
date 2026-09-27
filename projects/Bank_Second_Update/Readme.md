# 🏦 Bank Management System — C++ | Version 3

This is the **third update** of my C++ Bank Management System project.

In this version, I expanded the project by adding a complete **User Management and Access Control system**.

These new features came after studying the related concepts through **Programming Advices**, then applying what I learned directly to the existing project.

The goal was not just to learn the concepts theoretically, but to understand how they can be integrated into a real application.

---

## 🚀 What's New in Version 3?

### 🔐 1. Login & User Authentication

A login system has been added to control access to the application.

* Login using username and password
* Validate user credentials
* Load the authenticated user's information
* Keep track of the current logged-in user
* Handle invalid login attempts

---

### 👥 2. User Management

The system can now manage application users.

Users can:

* View all users
* Add new users
* Delete users
* Update user information
* Search for users
* Prevent duplicate usernames

The main `admin` account is also protected from deletion.

---

### 🛡️ 3. Permission-Based Access Control

One of the main additions in Version 3 is the **Permission System**.

Each user can have access to specific parts of the system instead of automatically having full access.

Available permissions include:

* List Clients
* Add New Client
* Delete Client
* Update Client
* Find Client
* Transactions
* Manage Users

The project uses **Bitwise Operations** to combine and check permissions.

This allowed me to understand how multiple permissions can be stored and checked efficiently.

---

### 🚪 4. Logout & Session Flow

The project now supports a complete login/logout flow.

After logging out:

**Current User → Logout → Login Screen → New Authentication Session**

This makes the application flow more similar to a real system where different users can authenticate and access the application according to their permissions.

---

## 👤 Existing Features

The features from the previous versions are still available.

### Client Management

* Display all clients
* Add new clients
* Delete clients
* Update client information
* Find clients by account number
* Prevent duplicate account numbers

### 💰 Transactions

* Deposit
* Withdraw
* Balance validation
* Calculate total balance

### 💾 File-Based Data Storage

The project uses text files to store data:

```text
Client.txt
User.txt
```

Records are converted between structured data and text using the custom separator:

```text
#//#
```

---

## 🧠 Concepts Applied

Through this version, I practiced and applied:

* C++
* Structs
* Functions
* Vectors
* Enums
* File Handling
* CRUD Operations
* Data Parsing
* Authentication
* Authorization
* Access Control
* Bitwise Operations
* Modular Programming
* Problem Solving

---

## 📚 Learning Source

The new User Management, Authentication, and Permission concepts were studied through **Programming Advices** and then implemented as part of this project.

This approach helped me move from simply understanding individual concepts to actually using them together inside a larger application.

---

## 🔄 Project Development

This project is being developed incrementally.

**Version 1 → Version 2 → Version 3**

Each update builds on the previous version while introducing new programming concepts and functionality.

Version 3 represents an important step in the project because the system is no longer focused only on client and transaction management — it now includes **users, authentication, authorization, and access control**.

---

## 🔮 Future Improvements

Some possible improvements for future versions:

* Improve password security
* Add transaction history
* Add transaction dates and timestamps
* Improve input validation
* Migrate from text files to a database
* Improve the user interface
* Add more advanced security features

---

## 👨‍💻 Author

**Mohamed Ayman**

Computer Engineering Student | Cybersecurity Learner | Penetration Testing Enthusiast

**GitHub:** `Eng-mohamedayman-cyber`

---

⭐ **This is Version 3 of the project — another step in my programming and problem-solving journey.**
