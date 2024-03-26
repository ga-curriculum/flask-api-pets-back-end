# ![Express REST API - Show Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a new route to find a single pet by interacting with the database and sending a JSON response to the client.

## READ - Show Route

In this section, we will create a new route to find a single pet. This route will be a `GET` request to `/pets/<pet_id>`, returning a JSON response with a single pet from the database.

We will be following these specs when building the route:

- **CRUD Action:** READ
- **Method:** `GET`
- **Path:** `/pets/<pet_id>`
- **Response:** JSON
- **Success Status Code:** 200
- **Success Response Body:** A JSON object with the pet that matches the `petId` parameter.
- **Error Status Code:** 404 || 500
- **Error Response Body:** A JSON object with an `error` key and a message describing the error.

Let's get started! 🎉

## Create a GET Route - Show

Inside `app.py`, create a new route to find a single pet. The **method** for this route is `GET`, and the **path** is `/pets/:petId`. Use the `@app.route()` decorator to create the route and pass in the path and the methods object:

```python
@app.route('/pets/<pet_id>', methods=['GET'])
def show_pet(pet_id):
    return f"Showing pet with id {pet_id}"
```
 ## Test the Route

 We've created the minimum testable route to ensure the paramter is working as intended. Send a request to `localhost:5000/pets/1` in Postman and ensure the response message is returned as intended.

 ## Adding the Database Query

 Inside a `try/except` block, we can now query for the pet with the given id.

 If the pet is found, return it.
 If the pet is not found, return a 404 "not found" error message.
 If any other error happens, return a 500 with the error message.

 ```python
 @app.route('/pets/<pet_id>', methods=['GET'])
def show_pet(pet_id):
  try:
    con = get_db_connection()
    cursor = con.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
    cursor.execute("SELECT * FROM pets WHERE id = %s", (pet_id,))
    pet = cursor.fetchone()
    if pet is None:
       return "Pet Not Found", 404
    cursor.close()
    return pet, 200
  except Exception as e:
     return str(e), 500
```

Note the comma in the `(pet_id,)` argument provided to our `execute` method. This ensures the value provided will be interpreted as a tuple, which is the required input type for paramaterized queries.

### Test the Route

Open Postman and send a `GET` request to `http://localhost:5000/pets/1`. You should receive a JSON response with the pet data as a single JSON object. 

Be sure to also test the failure case with a request to `http://localhost:5000/pets/4978` to receive a 404 response.

We now have our route for accessing a single record from our database!