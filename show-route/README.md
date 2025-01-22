<h1>
  <span class="headline">Pets Back End</span>
  <span class="subhead">Show Route</span>
</h1>

**Learning objective:** By the end of this lesson, students will be able to create a new route to find a single pet by interacting with the database and sending a JSON response to the client.

## READ - Show route

In this section, we will create a new route to find a single pet. This route will be a `GET` request to `/pets/<pet_id>`, returning a JSON response with a single pet from the database.

We will be following these specs when building the route:

- **CRUD Action:** READ
- **Method:** `GET`
- **Path:** `/pets/<pet_id>`
- **Response:** JSON
- **Success Status Code:** 200
- **Success Response Body:** A JSON object with the pet that matches the `petId` parameter.
- **Error Status Code:** 404 || 500
- **Error Response Body:** A JSON object with an error message describing the error.

Let's get started!

## Defining the route

Inside `app.py`, create a new route to find a single pet. The **method** for this route is `GET`, and the **path** is `/pets/:petId`. Use the `@app.route()` decorator to create the route and pass in the path and the methods object:

```python
# app.py

@app.route('/pets/<pet_id>', methods=['GET'])
def show_pet(pet_id):
    return f"Showing pet with id {pet_id}"
```

With the code above, we've created a testable route to ensure the `pet_id` parameter is working as intended. Send a request to `localhost:5000/pets/1` in Postman and ensure the response message includes the `pet_id`.

## Querying the database

We can now query the database for a pet using its unique identifier, `pet_id`. By implementing our query inside a `try/except` block, we can account for the following outcomes:

- **Pet Found:** In this scenario, we will return the pet's data along with a status code of `200`.
- **Pet Not Found:** If the resource cannot be located, we'll return a `404` "Not Found" error message.
- **Other Errors:** If any unaccounted issues arise in the `try`, we'll return a `500` status code along with an error message.

Add the following to `app.py`:

```python
# app.py
@app.route('/pets/<pet_id>', methods=['GET'])
def show_pet(pet_id):
    try:
        connection = get_db_connection()
        cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
        cursor.execute("SELECT * FROM pets WHERE id = %s", (pet_id,))
        pet = cursor.fetchone()
        if pet is None:
            connection.close()
            return "Pet Not Found", 404
        connection.close()
        return pet, 200
    except Exception as e:
        return str(e), 500
```

> 💡 Note the comma in the `(pet_id,)` argument provided to our `execute` method; this syntax ensures the value is treated as a tuple, which is necessary for parameterized queries to prevent SQL injection.

To test the route, open up Postman and send a `GET` request to `http://localhost:5000/pets/1`. You should receive a JSON response with the pet data as a single JSON object. You can also test the `404` failure scenario with a request to `http://localhost:5000/pets/4978`.
