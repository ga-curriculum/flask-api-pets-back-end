# ![Flask API - Pets Back-End - Delete Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a new route to delete a single pet by interacting with the database and sending a JSON response to the client.

## DELETE - Delete route

In this section, we will create a new route to delete a single pet. This route will be a `DELETE` request to `/pets/<pet_id>`, returning a status of `204` with no content.

We will be following these specs when building the route:

- **CRUD Action:** DELETE
- **Method:** `DELETE`
- **Path:** `/pets/<pet_id>`
- **Response:** No Content
- **Success Status Code:** 204
- **Error Status Code:** 404 || 500
- **Error Response Body:** A JSON object with an `error` key and a message describing the error.

## Defining the route

Inside `app.py`, create a new route for removing a single pet from the database. Our route should use the `pet_id` parameter in the route handler function object to access the `pet_id` from the path. If a pet has been deleted successfully, we'll return a `204` status, with the message `"Pet Deleted Successfully"`.

Add the following to `app.py`:

```python
# app.py
@app.route('/pets/<pet_id>', methods=['DELETE'])
def delete_pet(pet_id):
    try:
        connection = get_db_connection()
        cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
        cursor.execute("DELETE FROM pets WHERE id = %s", (pet_id,))
        if cursor.rowcount == 0:
            return "Pet not found", 404
        connection.commit()
        cursor.close()
        return "Pet deleted successfully", 204
    except Exception as e:
        return str(e), 500
```

With the route in place, let's test it out in Postman.

Open Postman and create a new request with the following details:

- Use the drop-down menu to select `DELETE` as the method.

- Enter the URL `http://localhost:5000/pets/<pet_id>` in the address bar, where `<pet_id>` is a valid pet id that exists in your database. If you need to create some more pets, use the `Create Route` we built earlier to add them to the database.

After you have sent the request, you should receive a status code of `204` with **no content in the response**. This means the pet was successfully deleted from the database.
