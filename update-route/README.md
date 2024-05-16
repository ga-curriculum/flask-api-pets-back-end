# ![Flask API - Pets Back-End - Update Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a new route to update a single pet by interacting with the database and sending a JSON response to the client.

## UPDATE - Update route

In this section, we will create a new route to update a single pet. This route will be a `PUT` request to `/pets/<pet_id>`, and will return a JSON response with the updated pet.

We will be following these specs when building the route:

- **CRUD Action:** UPDATE
- **Method:** `PUT`
- **Path:** `/pets/<pet_id>`
- **Response:** JSON
- **Success Status Code:** 202
- **Success Response Body:** A JSON object with the updated pet.
- **Error Status Code:** 500 || 404
- **Error Response Body:** A JSON object with a error message describing the error.

Let's get started! 🎉

## Defining the route

Let's create a new route to update a single pet. 

The **method** for this route is `PUT`, and the **path** is `/pets/<pet_id>`. Use the `@app.route()` decorator with the url path and the methods option to create the route.

Add the following to `app.py`:

```python
## app.py
@app.route('/pets/<pet_id>', methods=['PUT'])
def update_pet(pet_id):
    return f"Updating the pet with the id {pet_id}"
```

Before moving on, we should test this route to ensure it works.

Open Postman and send a `PUT` request to `http://localhost:5000/pets/1`. You should receive a JSON response with the `Updating the pet with the id 1` message.

## Updating a single pet in SQL

We have tested the route, so let's query the database and update a single pet. We can use an `UPDATE pets` SQL query including the `pet_id` for the WHERE clause and the values in `request.json` for the values to `SET`.

```python
@app.route('/pets/<pet_id>', methods=['PUT'])
def update_pet(pet_id):
    try:
      connection = get_db_connection()
      cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
      cursor.execute("UPDATE pets SET name = %s, age = %s, breed = %s WHERE id = %s RETURNING *", (request.json['name'], request.json['age'], request.json['breed'], pet_id))
      updated_pet = cursor.fetchone()
      if updated_pet is None:
        return "Pet Not Found", 404
      connection.commit()
      connection.close()
      return updated_pet, 202
    except Exception as e:
      return str(e), 500
```

Open up Postman and set up a PUT request to `/pets/<pet_id>`, including a JSON body with all the properties of a pet object: `name`, `age`, and `breed`.

You should receive the successfully updated object as a response.

 > 💡 If you try to update a a pet that doesn't exist or has been deleted, you should receive a `404` response. However, sending a request that doesn't contain **all** the fields we listed in our `UPDATE` statement, will produce a `500` error with the name of the missing properties. 