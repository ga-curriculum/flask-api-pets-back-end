# ![Express REST API - Update Route](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to create a new route to update a single pet by interacting with the database and sending a JSON response to the client.

## UPDATE - Update Route

In this section, we will create a new route to update a single pet. This route will be a `PUT` request to `/pets/:petId`, and will return a JSON response with the updated pet.

We will be following these specs when building the route:

- **CRUD Action:** UPDATE
- **Method:** `PUT`
- **Path:** `/pets/:petId`
- **Response:** JSON
- **Success Status Code:** 200
- **Success Response Body:** A JSON object with the updated pet.
- **Error Status Code:** 404 || 500
- **Error Response Body:** A JSON object with an `error` key and a message describing the error.

Let's get started! 🎉

## Create an UPDATE Route

Let's create a new route to update a single pet. The **method** for this route is `PUT`, and the **path** is `/pets/:petId`. Use the `.put()` method on the `router` object to create the route and pass in the path and a callback function.

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    // Setting up for our code
});
```

> 🧠 We still do not need to add `/pets` to the path because the route is already in the `/pets` route.

### Test the Route

Before moving on, we must test this route to ensure it works. We can do this by sending a response with a message to the client.

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    // Add a message to test the route
    res.json({ message: `Update route with the param ${req.params.petId}` });
});
```

Open Postman and send a `PUT` request to `http://localhost:3000/pets/1`. You should receive a JSON response with the `Update Route with the param 1` message. If you do not receive this message, debug before moving on.

### Try - Update a Single Pet

We have tested the route, so let's query the database and update a single pet. We can use the `.findByIdAndUpdate()` method on the `Pet` model. We'll use `await` to wait for the database to return the updated pet:

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    try {
        // Add query to update a single pet
        const foundPet = await Pet.findByIdAndUpdate(req.params.petId, req.body);
    } catch (error) {
        // Setup for error handling
    }
});
```

The`.findByIdAndUpdate()` method is similar to the `.findById()` method in that both return `null` if the query is not found in the database. As a result, we have to handle a `404` not found error if the pet is not in the database. Let's add this to the try block:

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    try {
        const foundPet = await Pet.findByIdAndUpdate(req.params.petId, req.body);
        // Add a check for a not found pet
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
    } catch (error) {
        // Setup for error handling
    }
});
```

Lastly, if we log the variable `foundPet` to the console, we will see the original pet *before* it was updated. We need to add an additional argument to the `.findByIdAndUpdate()` method to return the *updated* pet. Adding `{ new: true }` as the third argument will give us the updated resource, instead of the original. Once added, we can send a JSON response with the updated pet:

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    try {
        // Add { new: true } as the third argument
        const foundPet = await Pet.findByIdAndUpdate(req.params.petId, req.body, { new: true });
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
        // Add a JSON response with the updated pet
        res.json({ pet: foundPet });
    } catch (error) {
        // Setup for error handling
    }
});
```

## Catch - Errors

We have set up a `catch` block in the `try...catch` statement and coded for the case where a pet is not found. Let's handle any `404` Not found errors and make sure we are changing the status code to `500` for any other errors that might occur.

```js
// controllers/pets.js

// UPDATE - PUT - /pets/:petId
router.put('/:petId', async (req, res) => {
    try {
        const foundPet = await Pet.findByIdAndUpdate(req.params.petId, req.body, { new: true });
        if (!foundPet) {
            res.status(404);
            throw new Error('Pet not found.');
        }
        res.json({ pet: foundPet });
    } catch (error) {
        // Add code for errors
        if (res.statusCode === 404) {
            res.json({ error: error.message });
        } else {
            res.status(500).json({ error: error.message });
        }
    }
});
```

> 🧠 Remember we have to do the `if...else` statement because if any other error occurs, we want to set the status code to `500` and respond to the client with a message describing the error. If we do not change the status code, it will remain `200`, and the client might not know there was an error.

