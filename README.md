# Simple Database Library for SQLite and PostgreSQL

This Python library provides a simplified interface for interacting with SQLite and PostgreSQL databases. It allows you to easily connect to databases, query data, insert new records, update existing records, and delete records.

MySQL comming soon.

## Features

*   **Unified Interface:**  Consistent API for both SQLite and PostgreSQL databases.
*   **Table Object Mapping:**  Tables are automatically mapped as objects with methods for querying and manipulating data.
*   **Simplified Queries:**  Provides convenient methods for SELECT, INSERT, UPDATE, and DELETE operations.
*   **Output Formatting:**  Returns query results as objects with attributes corresponding to column names.
*   **Easy Connection:** Simplifies the connection process with connection string parameters.

## Installation

```
pip install sqlet
```
## Usage
### Connecting to a Database

```
from sqlet import db
```

#### SQLite
```
database = db(SUBD='sqlite', connection='path/to/your/database.db')
```

#### PostgreSQL
```
database = db(SUBD='postgre', connection='host+port+username+password+dbname')
```
#### Example
``` 
database = db(SUBD='postgre', connection='localhost+5432+user+password+your_db')
```


### Selecting Data

#### select() 

Filtering with Keyword Arguments

Select all columns from the 'users' table where age is 30 and city is 'London'
```
users = database.users.select(age=30, city='London')
```
or
```
users = database.select(table='users 'age=30, city='London')
```
Select specific columns name and email
```
users = database.users.select('name', 'email', age=25)
```
#### selectf()
Filtering with SQL

Select all columns from the 'users' table with a filter
```
users = database.users.selectf(filter="age > 25")
```
or
```
users = database.selectf(table='users', filter="city = 'New York'")
```
Select specific columns
```
users = database.selectf(table='users', columns=['name', 'email'], filter="city = 'New York' or age > 18")
```
#### select1() 
Getting a Single Result

Get the first user with the name 'John'
```
user = database.users.select1(name='John')
```
```
if user:
    print(user.email) # Accessing the email attribute of the user object
else:
    print("User not found")
```

### sqlet output object
As you can see, as a result of calling the selection functions, a special output object is returned

For each row returned by a query, an sqlet output object is created.  
The attributes of this object are dynamically created based on the column names of the table.

Assuming 'users' table has columns 'id', 'name', and 'email'
```
users = database.users.select(name='John') 
# returning array with output files
```
```
if users[0]:
    print(users[0].id)    # Access the 'id' column
    print(users[0].name)  # Access the 'name' column
    print(users[0].email) # Access the 'email' column
```

you can select just one object without array by using `select1()`
```
user = database.users.select1(name='John')
```


#### dict()
If you need the data as a dictionary, you can use the `dict()` method. This returns a dictionary where the keys are the column names and the values are the corresponding data.

```
user_dict = user.dict()
print(user_dict['name'])  # Accessing the 'name' using dictionary notation
```

#### update()
This method updates the corresponding row in the database. You provide a dictionary of column names and the new values you want to set.  The sqlet output object automatically handles the database connection and generates the appropriate SQL `UPDATE` statement.

```
user.update(email='john.doe.updated@example.com', age=31)  # Updates John's email and age
```

#### delete()
This method deletes the corresponding row from the database.

```
user.delete()  # Deletes the user named John from the database.
```


### Inserting Data
#### insert()

Insert a new user into the 'users' table
```
database.users.insert(name='Alice', email='alice@example.com', age=28)
```
or
```
database.insert('users', name='Bob', email='bob@example.com', age=32)
```
#### insertf()
A more complicated way of inserting
Insert data using column and value lists
```
database.insertf(table='users', columns=['name', 'email'], values=["'Charlie'", "'charlie@example.com'"])
```
### Updating Data
#### update()
_Not recommended_

Update the email address of a user with a specific name
```
database.users.update(filter="name = 'Alice'", values={'email': 'new_alice@example.com'})
```

#### Using output object to update
_recommended_
```
user = database.users.select1(name='John') # Get object
user.update(email='john.new@example.com') # Update email of that object
```

### Deleting Data

#### delete()

Delete a user with a specific ID
```
database.users.delete(id=123)
```
#### deletef()
Delete all users with age under 18
```
database.users.deletef(filter='age < 18')
```

#### Using output object to delete
```
user = database.users.select1(name='John') # Get object
user.delete() # Delete user
```

### Executing Raw SQL
#### sql()
```
results = database.sql("SELECT COUNT(*) FROM users")
print(results)
```
