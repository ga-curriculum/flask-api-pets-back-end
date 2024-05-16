# ![Flask API - Pets Back-End - Defining the Database](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to connect to a SQL Database and make queries from Python.

## Preparing the database

In this lesson, you we'll create a PostgreSQL database using the `psql` command line tool.

Open your terminal and run the following command:

```bash
psql
```

Next, we'll initialize a new database for this project:

```sql
CREATE DATABASE pets_db;
```

To connect to the new database, run the following:

```sql
\c pets_db;
```

After running the command above, you should see something like the following in your terminal:

```plaintext
You are now connected to database "pets_db" as user "<your-user-name>"
```

Now that we are connected to the database we can create a table for our pets data:

```SQL
CREATE TABLE pets(
  id serial PRIMARY KEY,
  name varchar(50) NOT NULL,
  age integer NOT NULL,
  breed varchar(50) NOT NULL
);
```

Let's add some data to the table. Run the following:

```sql
INSERT INTO pets (name, age, breed) VALUES ('Fido', 2, 'beagle');
```

Next we'll make a a query to confirm the insertion worked as expected:

```SQL
SELECT * FROM pets;
```

After running the command above, you should the see the following:

```plaintext
 id | name | age | breed  
----+------+-----+--------
  1 | Fido |   2 | beagle
(1 row)
```

## Connecting to PostgreSQL from our Flask app

In our initial setup, we installed `psycopg2`, a popular PostgreSQL Python driver, and `python-dotenv` for storing our connection credentials securely in a `.env` file. Let's make use of these packages in our Flask application now.

First create the `.env` file:

```bash
touch .env
```

Within the `.env`, add your local PostgreSQL credentials like so:

```plaintext
POSTGRES_USER=<your-local-user-name-for-postgres>
POSTGRES_PASSWORD= <your-local-password-for-postgres>
```

To access these environment variables, we'll need to add an import in `app.py`:

```python
# app.py
from dotenv import load_dotenv
```

Next we'll call upon `load_dotenv` directly above the initialization of our Flask app:

```python
# app.py
# Add the following:
load_dotenv()

app = Flask(__name__)
```

> 💡 By default, `load_dotenv()` looks for a file named `.env` in the current directory. It loads the environment variables defined in that file, and makes them accessible through `os.environ`. 

With our environment variables accessible, we can build out a connection to our database. We can consult the [Psycopg documentation](https://www.psycopg.org/docs/usage.html) for basic guidance on connecting to our PostgreSQL database from Python.

First, add imports for `psycopg2` and `os` at the top of `app.py`:

```python
# app.py
import os
import psycopg2
```

Next, add the following function:

```python
# app.py
def get_db_connection():
    connection = psycopg2.connect(
        host='localhost',
        database='pets_db',
        user=os.environ['POSTGRES_USER'],
        password=os.environ['POSTGRES_PASSWORD']
    )
    return connection
```

> 💡 Our `get_db_connection()` will  be used to establish and return a connection to our PostgreSQL database. We'll need to call upon this whenever we wish to interact with our database within Flask.

Next, within an `index` route, we'll make use of our database connection to execute a `SELECT` on the `pets` table.

In `app.py`, add the following `index` route:

```python
# app.py
@app.route('/pets')
def index():
  connection = get_db_connection()
  cursor = connection.cursor()
  cursor.execute("SELECT * FROM pets;")
  pets = cursor.fetchall()
  connection.close()
  return pets
```

In Postman (or your browser), make a `GET` request to `http://127.0.0.1:5000/pets`. 

You should see something like the following as a response:

```plaintext
[[1,"Fido",2,"beagle"]]
```

## Implementing `connection` and `cursor`

Let's take a moment to discuss the implementation of `get_db_connection()` in the above code. 

Notice the use of **`connection`** and **`cursor`**:

- **`connection`:** When interacting with a database, this `connection` acts as a direct line to the database itself.

- **`connection`:** The `cursor` is a tool for executing and retrieving data with SQL commands.

In our code, we first establish a `connection` to the database. With our `connection`, we create new `cursor` instance using the `cursor()` method:

```python
  connection = get_db_connection()
  cursor = connection.cursor()
```

This `cursor` instance includes several methods for interacting with the corresponding database. The `.execute()` method for example, accepts raw SQL commands. Here, we use it to `SELECT` everything from the `pets` table:

```python
  cursor.execute("SELECT * FROM pets;")
```

After executing a query with `cursor.execute()`, the cursor object doesn't immediately load all the results, but it does maintain a connection to the result set that the database has prepared. The actual data remains on the database server, and the cursor acts as a pointer to this result set.

To retrieve the dataset, we utilize methods like `cursor.fetchall()`, `cursor.fetchone()`, or `cursor.fetchmany()`:

- **`cursor.fetchall()`**: Retrieves all rows of the query result.
- **`cursor.fetchone()`**: Retrieves the next row of the query result.
- **`cursor.fetchmany(size)`**: Retrieves the next set of rows defined by size.

These methods pull the data from the database server into our application, where we can work with it directly.

In our code, we call upon `cursor.fetchall()`, to store the results of the query in the `pets` variable:

```python
  pets = cursor.fetchall()
```

The final aspect of our code to take note of is the use `connection.close()`. This method is used to close our connection to the database. If you don't close the database connection in a request, connections can accumulate over time. Eventually, this can cause your application to slow down or even fail to handle new requests.

```python
  connection.close()
```

> 🚨 Always be sure to close your connection at the end of a route. Closing a connection ends your session with the database, releasing all resources linked to that session, including any cursors you might have forgotten to close. This is like hanging up a phone after a call, ensuring no resources are left hanging, which could affect future database interactions.

## Formatting our data as JSON

In the Postman response from earlier, you might notice a odd format to the data. What we have here is a **list of lists**, excluding the the column names from our table. This format is less than ideal for an API.

We need something that will structure this SQL response into an array of JSON objects that users would expect when iteracting with an API. Fortunately, `psycopg2` has a built-in option called `cursor_factory` that will allow us to define the format of query results.

At the top of `app.py`, modify your existing `import` for `psycopg2`:

```python
import psycopg2, psycopg2.extras
```

Next, in your `index` route, update the `cursor` initialization to include a `cursor_factory` option, like so:

```python
# app.py
  # index route function
	cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
```

Restart your server and issue the request once again.

Your response should now be more in line with the conventions of a REST API:

```json
[{"age":2,"breed":"beagle","id":1,"name":"Fido"}]
```

We're now ready to start defining our RESTful routes for the pets resource!