# 🏦 Bank Management System with Transactions – C++

A console-based **Bank Management System** developed using C++ as part of my practical learning journey with **Programming Advices**.

## 📌 Project Overview

This project is an expanded version of a client management system.

It allows users to manage client records and perform financial transactions such as **deposits, withdrawals, and total balance calculations**.

Client data is stored in a text file, allowing the information to persist after the program is closed.

## 🎯 Main Features

### Client Management

* Show all clients
* Add new clients
* Delete clients
* Update client information
* Find clients by account number
* Prevent duplicate account numbers

### Transactions

* Deposit money
* Withdraw money
* Prevent withdrawals exceeding the available balance
* Calculate the total balance of all clients
* Display updated account balances
* Confirmation before performing transactions

### Data Management

* Load client data from a file
* Save updated client data back to the file
* Convert text lines into C++ records
* Convert C++ records into text lines

## 🛠️ C++ Concepts Practiced

* Structs
* Enums
* Vectors
* Functions
* References
* File Handling
* CRUD Operations
* `fstream`
* `stod()`
* String Manipulation
* Loops
* Conditional Statements
* `switch`
* Input Validation
* Modular Programming
* Problem Solving

## 🔄 CRUD Operations

* **Create** → Add new clients
* **Read** → Display and find clients
* **Update** → Modify client information
* **Delete** → Remove clients

## 💰 Transaction Operations

### Deposit

The user selects an account and enters a deposit amount. The amount is added to the client's current balance and saved to the file.

### Withdraw

The system checks the requested amount against the current balance before completing the transaction.

If the requested amount exceeds the available balance, the user is asked to enter another amount.

### Total Balance

The system displays the balance of every client and calculates the combined total balance.

## 📂 Data Storage

Client information is stored in:

`Client.txt`

Records use the following separator:

`#//#`

Example structure:

`AccountNumber#//#PinCode#//#Name#//#PhoneNumber#//#AccountBalance`

## 📚 Learning Context

This project is part of my **C++ learning journey with Programming Advices**.

Through this project, I practiced combining multiple programming concepts into a larger application, including **file handling, CRUD operations, data structures, and transaction logic**.

## 💻 Technologies
