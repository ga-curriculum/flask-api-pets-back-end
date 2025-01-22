<h1>
  <span class="headline">Pets Back End</span>
  <span class="subhead">Concepts</span>
</h1>

**Learning objective:** By the end of this lesson, students will understand RESTful APIs.

## REST APIs

A web API (application programming interface) is an interface that facilitates communication between clients and resources. An API acts as an intermediary between two applications; for instance, allowing a front end to communicate with a server and send back requested data. 

As APIs have become increasingly prevalent and designed for consumption, various API protocols have been established to help provide users with a set of defined rules and expectations. One of the most popular of these API protocols is REST. 

REST, short for REpresentational State Transfer, is a guiding architectural style for web applications, focusing on the client-server relationship. It's a set of principles that simplifies how web resources are defined and addressed. RESTful APIs are both developer-friendly and intuitive to users, given that their structure is broadly understood and uniform. 

For this lesson, we'll be creating a RESTful API that handles [CRUD](https://developer.mozilla.org/en-US/docs/Glossary/CRUD) of a Pets resource. Using restful routing conventions, we can expect the following: 

| HTTP Method | Controller  | Response | URI            | Use Case         |
|-------------|-------------|----------|----------------|------------------|
| POST        | `create`    | 200      | `/pets`        | Create a pet     |
| GET         | `index`     | 200      | `/pets`        | List pets        |
| GET         | `show`      | 200      | `/pets/:petId` | Get a single pet |
| PUT         | `update`    | 200      | `/pets/:petId` | Update a pet     |
| DELETE      | `deletePet` | 204      | `/pets/:petId` | Delete a pet     |
