# ![Flask API - Pets Back-End - CORS](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to implement Cross-Origin Resource Sharing (CORS) in an Express application to allow the API to be accessed by other domains.

## CORS

We have created a RESTful API, but we still have one more thing to do before we can call it complete. We need to handle [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) to **allow our API to be accessed by other domains**. 

But first what is CORS and why do we need it?

Let's journey back in time to the early days of the internet. Websites were static and the only way to interact with them was to click on links. As the internet grew, so did the need for more dynamic and interactive websites. This led to the creation of the [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) object, which allowed websites to make requests to servers without having to reload the page. This was the birth of [AJAX (Asynchronous JavaScript and XML)](https://developer.mozilla.org/en-US/docs/Glossary/AJAX), and it was the beginning of the modern web.

With the advent of AJAX, rules were needed to govern how websites interact with each other, leading to the development of CORS.

CORS is a security feature that restricts how a web application running on one domain can interact with resources from another domain. It is a set of rules that a server can use to allow or deny access to its resources.

Let's take this into the real world for a second. Think of Amazon or another large plateform. Amazon has a back end with many many many end points. Think of our application we just built but now enhance that to the size of Amazon. That is a lot of end points. Now think of all the different ways people can access Amazon. They can use the website, the mobile app, the Alexa app, the Echo, the Fire Stick, and so on. Each of these different ways to access Amazon is a different domain. So Amazon has to allow all of these different domains to access their resources.

Now by default, browsers restrict cross-origin HTTP requests initiated from scripts. This means that if you try to make a request from a different domain, the browser will block the request. This is where CORS comes in. CORS allows the server to specify who can access its resources. This is done by adding a special header to the response that the server sends back to the client. This header is called `Access-Control-Allow-Origin`. This header tells the browser which domains are allowed to access the server's resources.

For our Flask application to be used by things other than postman, we'll need to implment cors.

## Implement CORS in Flask

To implement CORS in our Flask application, we'll first need to install the `flask-cors` package.

Run the following command in your terminal:

```bash
pipenv install flask-cors
```

Next, in `app.py`, add an import for `flask-cors`:

```python
# app.py
from flask import Flask, request
# New import:
from flask_cors import CORS
```

The `flask-cors`[documentation](https://flask-cors.readthedocs.io/en/latest/) can give us an idea of how add this module to our Flask app. We'll need invoke the provided `CORS` function on the `app` object in `app.py`. 

Add the following `CORS(app)` directly below the initialization of `app`:

```python
# app.py
app = Flask(__name__)
# New code:
CORS(app)
```

And there it is! 

> 💡 Note, for a larger application, we'd want to research the available options provided in the `flask-cors`[documentation](https://flask-cors.readthedocs.io/en/latest/). For now, however, this code is enough to make our API accessible to a basic client app.