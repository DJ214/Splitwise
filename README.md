
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

## API Contracts

### User Management

#### 1. Create a user

- **Endpoint:** `POST /api/users/`
- **Request:**
  ```json
  {
    "name": "abc def",
    "email": "abc@example.com",
    "mobileNumber": "1234567890"
  }
  ```
- **Response:**
  ```json
  {
  "userId": 1,
  "name": "abc def",
  "email": "abc@example.com",
  "mobileNumber": "1234567890"
  }
  ```
#### 2. Get user details
- **Endpoint:** `GET /api/users/{userId}/`
- **Response:**
  ```json
  {
  "userId": 1,
  "name": "abc def",
  "email": "abc@example.com",
  "mobileNumber": "1234567890"
  }
  ```
#### 3. Update user details
- **Endpoint:** `PUT /api/users/{userId}/`
- **Request:**
  ```json
  {
  "name": "Updated Name",
  "email": "updated@example.com",
  "mobileNumber": "9876543210"
  }
  ```
- **Response:**
  ```json
  {
  "userId": 1,
  "name": "Updated Name",
  "email": "updated@example.com",
  "mobileNumber": "9876543210"
  }
  ```
### Expense Management
#### 4. Create an expense
- **Endpoint:** `POST /api/expenses/`
- **Request:**
  ```json
  {
  "name": "Electricity Bill",
  "totalAmount": 1000,
  "createdBy": 1,
  "expenseType": "EQUAL",
  "expenseDate": "2023-11-18",
  "isSimplified": true,
  "participants": [1, 2, 3, 4]
  }
  ```
- **Response:**
  ```json
  {
  "expenseId": 1,
  "name": "Electricity Bill",
  "totalAmount": 1000,
  "createdBy": 1,
  "expenseType": "EQUAL",
  "expenseDate": "2023-11-18",
  "isSimplified": true,
  "participants": [1, 2, 3, 4]
  }
  ```
#### 5. Get expense details
- **Endpoint:** `GET /api/expenses/{expenseId}/`
- **Response:**
  ```json
  {
  "expenseId": 1,
  "name": "Electricity Bill",
  "totalAmount": 1000,
  "createdBy": 1,
  "expenseType": "EQUAL",
  "expenseDate": "2023-11-18",
  "isSimplified": true,
  "participants": [1, 2, 3, 4]
  }
  ```
#### 6. Update expense details
- **Endpoint:** `PUT /api/expenses/{expenseId}/`
- **Request:**
  ```json
  {
  "name": "Updated Expense Name",
  "totalAmount": 1200,
  "expenseType": "EXACT",
  "participants": [1, 2, 3]
  }
  ```
- **Response:**
  ```json
  {
  "expenseId": 1,
  "name": "Updated Expense Name",
  "totalAmount": 1200,
  "createdBy": 1,
  "expenseType": "EXACT",
  "expenseDate": "2023-11-18",
  "isSimplified": true,
  "participants": [1, 2, 3]
  }
  ```
  
#### 7. Add a transaction to an expense
- **Endpoint:** `POST /api/expenses/{expenseId}/add-transaction/`
- **Request:**
  ```json

  {
  "payer": 1,
  "payee": 2,
  "amount": 250
  }
  ```
  
- **Response:**
  ```json

  {
  "transactionId": 1,
  "payer": 1,
  "payee": 2,
  "amount": 250
  }
  ```

#### 8. Get all transactions for an expense
- **Endpoint:** `GET /api/expenses/{expenseId}/transactions/`
- **Response:**
  ```json
  [
  {
    "transactionId": 1,
    "payer": 1,
    "payee": 2,
    "amount": 250
  },
  // Additional transactions...
  ]
  ```

#### 9. Get user's passbook
- **Endpoint:** `GET /api/users/{userId}/passbook/`
- **Response:**
  ```json

  [
  {
    "transactionId": 1,
    "expenseId": 1,
    "name": "Electricity Bill",
    "amount": -250,
    "date": "2023-11-18"
  },
  // Additional passbook entries...
  ]
  ```
### Balance Management

#### 10. Get balances for a user
- **Endpoint:** `GET /api/balances/{userId}/`
- **Response:**
  ```json
  {
  "user": {
    "userId": 1,
    "name": "abc"
              }
  }
  ```

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
