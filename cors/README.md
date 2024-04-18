# ![Express REST API - CORS](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement Cross-Origin Resource Sharing (CORS) in an Express application to allow the API to be accessed by other domains.

## CORS

We have created a RESTful API, but we still have one more thing to do before we can call it complete. We need to handle Cross-Origin Resource Sharing (CORS) to allow our API to be accessed by other domains. But first what is CORS and why do we need it?

Let's journey back in time when the internet was but a wee lab. Websites were static and the only way to interact with them was to click on links. But as the internet grew, so did the need for more dynamic and interactive websites. This led to the creation of the XMLHttpRequest object, which allowed websites to make requests to servers without having to reload the page. This was the birth of AJAX (Asynchronous JavaScript and XML), and it was the beginning of the modern web.

With the birth of AJAX we needed rules to govern how websites could interact with each other, so the birth of CORS happened. CORS is a security feature that restricts how a web application running on one domain can interact with resources from another domain. It is a set of rules that a server can use to allow or deny access to its resources.

Let's take this into the real world for a second. Think of Amazon or another large plateform. Amazon has a back end with many many many end points. Think of our application we just built but now enhance that to the size of Amazon. That is a lot of end points. Now think of all the different ways people can access Amazon. They can use the website, the mobile app, the Alexa app, the Echo, the Fire Stick, and so on. Each of these different ways to access Amazon is a different domain. So Amazon has to allow all of these different domains to access their resources.

Now by default, browsers restrict cross-origin HTTP requests initiated from scripts. This means that if you try to make a request from a different domain, the browser will block the request. This is where CORS comes in. CORS allows the server to specify who can access its resources. This is done by adding a special header to the response that the server sends back to the client. This header is called `Access-Control-Allow-Origin`. This header tells the browser which domains are allowed to access the server's resources.


## Implement CORS in Flask

First we need to install the `flask-cors` package. This package will allow us to easily implement CORS in our Flask application.

```bash
pipenv install flask-cors
```

We can consult the [documentation](https://flask-cors.readthedocs.io/en/latest/) on how to easily drop this module into our Flask app.

Simply invoke the provided `CORS` function on the `app` object in `app.py` before proceeding to define our routes, like so:

```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)
```

And there it is! Of course, we'd want to use the many options available to us in the documentation once we're sure of what front-end applications will be making requests to our API. For now, however, this will make our API accessible and testable from any other application.

