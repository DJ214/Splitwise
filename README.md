## Splitwise
System Design
Architecture Diagram
The expense-sharing system follows a three-tier architecture with the presentation layer, business logic layer, and data access layer.

sql
Copy code
   +---------------------+        +-----------------------+        +------------------------+
   |      Views          |  <---  |      Views/Logic      |  <---  |       Models/DB         |
   +---------------------+        +-----------------------+        +------------------------+
   |   HTML Templates    |        |    Business Logic     |        |   Database Models      |
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
API Contracts
User Management
Create User
Endpoint: /api/users/
Method: POST
Request Body:
json
Copy code
{
  "name": "John Doe",
  "email": "john@example.com",
  "mobile_number": "1234567890"
}
Get User Details
Endpoint: /api/users/{user_id}/
Method: GET
Expense Management
Create Expense

Endpoint: /api/expenses/
Method: POST
Request Body:
json
Copy code
{
  "name": "Electricity Bill",
  "total_amount": 1000,
  "created_by": 1,
  "expense_type": "EQUAL",
  "expense_date": "2023-11-18",
  "is_simplified": true,
  "participants": [1, 2, 3, 4]
}
Get Expense Details

Endpoint: /api/expenses/{expense_id}/
Method: GET
Add Transaction to Expense

Endpoint: /api/expenses/{expense_id}/add-transaction/
Method: POST
Request Body:
json
Copy code
{
  "payer": 1,
  "payee": 2,
  "amount": 250
}
Balance Management
Get Balances for a User

Endpoint: /api/balances/{user_id}/
Method: GET
Simplify Balances

Endpoint: /api/balances/simplify/
Method: POST
Class Structure
User Class
python
Copy code
class User(models.Model):
    name = models.CharField(max_length=255)
    email = models.EmailField()
    mobile_number = models.CharField(max_length=20)
Expense Class
python
Copy code
class Expense(models.Model):
    name = models.CharField(max_length=255)
    total_amount = models.FloatField()
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)
    expense_type = models.CharField(max_length=10)  # EQUAL, EXACT, PERCENT
    expense_date = models.DateField()
    is_simplified = models.BooleanField()
    participants = models.ManyToManyField(User, through='Transaction')
Transaction Class
python
Copy code
class Transaction(models.Model):
    expense = models.ForeignKey(Expense, on_delete=models.CASCADE)
    payer = models.ForeignKey(User, on_delete=models.CASCADE, related_name='payer_transactions')
    payee = models.ForeignKey(User, on_delete=models.CASCADE, related_name='payee_transactions')
    amount = models.FloatField()
