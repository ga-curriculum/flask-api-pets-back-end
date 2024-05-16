# ![Flask API - Pets Back-End - Index Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students can create an index route to find all pets by interacting with the database and send a JSON response to the client.

## READ - Index route

In this section, we will create a new route to find all pets. This route will be a `GET` request to `/pets`, and will return a JSON response with all the pets in the database.

We will be following these specs when building the route:

- **CRUD Action:** READ
- **Method:** `GET`
- **Path:** `/pets`
- **Response:** JSON
- **Success Status Code:** 200
- **Success Response Body:** An array of all the pets in the database named `pets`. The array will be empty if there are no pets in the database.
- **Error Status Code:** 500
- **Error Response Body:** A JSON object with an `error` key and a message describing the error.

Let's get started!

## Defining the route

Fortunately, we already built and tested this query when setting up our database connection! If you'd like to verify the route, you should be able to make a Postman request to `localhost:5000/pets` and receive the same JSON array response as before.

## Error Handling

Before moving on, let's add some error handling to our route. Adding error handling to an API is helpful, as it allows unexpected issues to be handled gracefully. Problems can arise from various sources, such as database connectivity issues, data retrieval errors, or runtime exceptions. 

For error handling in our index route, we'll add a `try/except` block to send an error as a response if anything goes wrong:

```python
@app.route('/pets')
def pets_index():
  try:
    connection = get_db_connection()
    cursor = connection.cursor(cursor_factory=psycopg2.extras.RealDictCursor)
    cursor.execute("SELECT * FROM pets;")
    pets = cursor.fetchall()
    connection.close()
    return pets
  except:
     return "Application Error", 500
```

## 🎓 You Do: Test the Index route with Postman

Now that we have finished the route let's test it with Postman. Send a `GET` request to `http://localhost:5000/pets`. Test both **success** and **error** messages.

- **Success:** You should receive a JSON response with all the pets in the database.
- **Error:** You should receive a JSON response with an error message.

> 🧠 If you are unsure how to test the failure message here, you can simulate an error by creating one yourself. You can add a `raise Exception("Application Error")` statement on the first line of the `try` block. This will throw an error, triggering the `except` block. This is only for testing purposes and should be removed once you have tested the error message.