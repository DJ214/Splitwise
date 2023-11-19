
# Splitwise

## Architecture Overview

### Architecture Diagram

The expense-sharing system follows a three-tier architecture with the presentation layer, business logic layer, and data access layer.

```sql
+---------------------+        +-----------------------+        +------------------------+
|      Controller     |  <---  |      Service Layer    |  <---  |       Data Access      |
+---------------------+        +-----------------------+        +------------------------+
|   API Endpoints     |        |    Business Logic     |        |   Database Queries     |
+---------------------+        +-----------------------+        +------------------------+
            |                             |                                 |
            |                             |                                 |
            +-------------------------------------------------------------+
                                          |
                                          v
                                   +--------------+
                                   |    Models    |
                                   +--------------+
                                   | User         |
                                   | Expense      |
                                   | Transaction  |
                                   +--------------+


```

## Database Schema

### User Table

- `userId` (Primary Key)
- `name`
- `email`
- `mobileNumber`

### Expense Table

- `expenseId` (Primary Key)
- `name`
- `totalAmount`
- `createdBy` (Foreign Key referencing User)
- `expenseType` (ENUM: EQUAL, EXACT, PERCENT)
- `expenseDate`
- `isSimplified` (Boolean)

### Transaction Table

- `transactionId` (Primary Key)
- `expenseId` (Foreign Key referencing Expense)
- `payerId` (Foreign Key referencing User)
- `payeeId` (Foreign Key referencing User)
- `amount`


## API Endpoints

### User Management

- `POST /api/users/` - Create a user
- `GET /api/users/{userId}/` - Get user details
- `PUT /api/users/{userId}/` - Update user details

### Expense Management

- `POST /api/expenses/` - Create an expense
- `GET /api/expenses/{expenseId}/` - Get expense details
- `PUT /api/expenses/{expenseId}/` - Update expense details
- `POST /api/expenses/{expenseId}/add-transaction/` - Add a transaction to an expense
- `GET /api/expenses/{expenseId}/transactions/` - Get all transactions for an expense
- `GET /api/users/{userId}/passbook/` - Get user's passbook

### Balance Management

- `GET /api/balances/{userId}/` - Get balances for a user
- `POST /api/balances/simplify/` - Simplify balances

## Class Structure

### User Class

```python
class User:
    userId
    name
    email
    mobileNumber
```
### Expense Class

```python
class Expense:
    expenseId
    name
    totalAmount
    createdBy (User)
    expenseType
    expenseDate
    isSimplified
    participants (List of Users)
    transactions (List of Transactions)
```
### Transaction Class

```python
class Transaction:
    transactionId
    expenseId
    payer (User)
    payee (User)
    amount
```
