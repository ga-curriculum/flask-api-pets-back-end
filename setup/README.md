# ![[tktk Module Name] - Setup](./assets/hero.png)

## Setup

Open your Terminal application and navigate to your **`~/code/ga/lectures`** directory:

```bash
cd ~/code/ga/lectures
```

Make a new directory called **`[tktk-module-name]`**, then enter this directory:

```bash
mkdir [tktk-module-name]
cd [tktk-module-name]
```

<!-- Other setup instructions here -->

Add the simplest Flask app boilerplate to `app.py`:

```python
# Import the 'Flask' class from the 'flask' library.
from flask import Flask

# Initialize Flask
# We'll use the pre-defined global '__name__' variable to tell Flask where it is.
app = Flask(__name__)

# Define our route
# This syntax is using a Python decorator, which is essentially a succinct way to wrap a function in another function.
@app.route('/')
def index():
  return "Hello, world!"

# Run our application, by default on port 5000
app.run()
```

Run the application from the terminal:

```bash
python app.py
```

And send a test request in your browser to localhost:5000 to hit the index route.

Open the contents of the directory in VSCode:

```bash
code .
```