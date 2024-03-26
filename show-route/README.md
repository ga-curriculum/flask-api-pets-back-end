# ![Express REST API - Show Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a new route to find a single pet by interacting with the database and sending a JSON response to the client.

## READ - Show Route

In this section, we will create a new route to find a single pet. This route will be a `GET` request to `/pets/:petId`, returning a JSON response with a single pet from the database.

We will be following these specs when building the route:

- **CRUD Action:** READ
- **Method:** `GET`
- **Path:** `/pets/:petId`
- **Response:** JSON
- **Success Status Code:** 200
- **Success Response Body:** A JSON object with the pet that matches the `petId` parameter.
- **Error Status Code:** 404 || 500
- **Error Response Body:** A JSON object with an `error` key and a message describing the error.

Let's get started! 🎉

## Create a GET Route - Show

Inside `controllers/pets.js`, create a new route to find a single pet. The **method** for this route is `GET`, and the **path** is `/pets/:petId`. Use the `.get()` method on the `router` object to create the route and pass in the path and a callback function.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    // Setting up for our code
});
```

Since we are already in the `/pets` route, we can use `/:petId` as the path. This will allow us to access the `petId` parameter from the `req.params` object.

### Test the Route

Before moving on, we must test this route to ensure it works. We can do this by sending a response with a message to the client.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    // Add a message to test the route
    res.json({ message: `Show route with the param ${req.params.petId}` });
});
```

Open Postman and send a `GET` request to `http://localhost:3000/pets/1`. You should receive a JSON response with the message `Show Route with the param 1`. If you do not receive this message, debug before moving on.

### Try - Find a Single Pet

We have tested the route, so let's query the database and find a single pet. We can do this using the `.findById()` method on the `Pet` model. We will use `await` to wait for the database to return the pet. We'll wrap this in a `try...catch` block to handle any errors that may occur.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    try {
        // Add query to find a single pet
        const foundPet = await Pet.findById(req.params.petId);
        res.json({ pet: foundPet });
    } catch (error) {
       // Setup for error handling
    }
});
```

Like in our test above, we use the `req.params.petId` to access the `petId` parameter from the `req.params` object. However, we still need to handle cases where the `petId` does not exist in the database. 

In Mongoose, if a document is not found, the `.findById()` method will return `null` but not throw an error. We need to handle this case and send the client a `404` status code with a message.

We can do this by checking if `foundPet` is `null` - if so, we set the status to `404` and throw a new error with the message `Pet not found`.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    try {
        const foundPet = await Pet.findById(req.params.petId);
        // Add error handling if a pet is not found
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
        res.json({ pet: foundPet });
    } catch (error) {
        // Setup for error handling
    }
});
```

> 📚 Since `res.status` just sets the status of the current response object, we need a way to stop the function's execution. We can do this by throwing a new error with the `Pet not found` message. This throw will be caught in the `catch` block, which sends a response to the client.

## Catch - Errors

We have set up a `catch` block in the `try...catch` statement and coded for the case where a pet is not found. However, we need to handle any other errors that might occur. 

First, we want to see if the error we are catching is a `404` status code. If this error occurs, we have already set the status code, so we just need to send a response to the client.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    try {
        const foundPet = await Pet.findById(req.params.petId);
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
        res.json({ pet: foundPet });
    } catch (error) {
        // Add error handling code for 404 errors
        if (res.statusCode === 404) {
            res.json({ error: error.message });
        }
    }
});
```

Now, we need to handle any other errors that might occur. We can do this by setting the status code to `500` and sending a response to the client with a message describing the error.

```js
// controllers/pets.js

// READ - GET - /pets/:petId
router.get('/:petId', async (req, res) => {
    try {
        const foundPet = await Pet.findById(req.params.petId);
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
        res.json({ pet: foundPet });
    } catch (error) {
        if (res.statusCode === 404) {
            res.json({ error: error.message });
        } else { // Add else statement to handle all other errors
            res.status(500).json({ error: error.message });
        }
    }
});
```

Returning a descriptive status code is very helpful, as without this step a client could receive a `200` status code ('OK') despite a server error. Now, any error message will be accompanied by a `500` status code (Internal Server Error) which clarifies for the client that their request was unsuccessful. This will also be semantically different from not being able to find the requested resource, which returns a `404`. 


## 🎓 You do: Test the Index Route with Postman

Use Postman to test the new route. Open Postman and send a `GET` request to `http://localhost:3000/pets/<petId>`. Test both **success** and **error** cases.

- **Success:** You should receive a JSON response with the pet that matches the `petId` parameter.
- **Error**: You should receive a JSON response with an `error` key and a message describing the error.

To get a valid `petId`, you can use the Index request to find all pets and use the `id` of one of the pets.

> 🧠 To test the `404` Not Found case, use a `petId` that does not exist in the database. You can do this by changing the last digit of the `petId` to a number that does not yet exist in the database.