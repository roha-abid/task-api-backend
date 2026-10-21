# Task Manager REST API

A backend REST API built with **Node.js, Express.js and MongoDB (Mongoose)**. It supports full CRUD operations on tasks. Built for Week 3 of the AUREX internship.

**Author:** [Roha Abid]

## Tech Stack

- Node.js
- Express.js
- MongoDB Atlas
- Mongoose
- dotenv, cors, nodemon

## Setup

1. Clone the repository:

```
   git clone https://github.com/roha-abid/task-api-backend.git
   cd task-api-backend
```

2. Install dependencies:

```
   npm install
```

3. Create a `.env` file in the project root (copy `.env.example`) and add your own values:

```
   PORT=5000
   MONGODB_URI=your_mongodb_atlas_connection_string
   JWT_SECRET=your_secret
```

4. Start the server:

```
   npm run dev
```

5. The API runs at `http://localhost:5000`.

> `.env` is listed in `.gitignore` and is never uploaded to GitHub.

## API Endpoints

Base URL: `http://localhost:5000/api/tasks`

| Method | Endpoint         | Description                                | Success | Error codes |
| ------ | ---------------- | ------------------------------------------ | ------- | ----------- |
| POST   | `/api/tasks`     | Create a task                              | 201     | 400         |
| GET    | `/api/tasks`     | Get all tasks (optional `?status=pending`) | 200     | -           |
| GET    | `/api/tasks/:id` | Get a single task                          | 200     | 400, 404    |
| PUT    | `/api/tasks/:id` | Update a task                              | 200     | 400, 404    |
| DELETE | `/api/tasks/:id` | Delete a task                              | 200     | 400, 404    |

### Example: create a task

Request: `POST /api/tasks`

```json
{
  "title": "Learn Express",
  "description": "Finish week 3 task",
  "priority": "high"
}
```

Response (201 Created):

```json
{
  "title": "Learn Express",
  "description": "Finish week 3 task",
  "status": "pending",
  "priority": "high",
  "_id": "6ad7aee2dff418c5063a4929",
  "createdAt": "2026-10-20T18:11:46.628Z",
  "updatedAt": "2026-10-20T18:11:46.628Z"
}
```

### Status codes used

| Code | Meaning      | When                              |
| ---- | ------------ | --------------------------------- |
| 200  | OK           | Successful read, update or delete |
| 201  | Created      | Task created                      |
| 400  | Bad Request  | Validation failed or invalid ID   |
| 404  | Not Found    | Task or route does not exist      |
| 500  | Server Error | Unexpected error                  |

## Database and Model

- **Database:** MongoDB Atlas (cloud, free M0 cluster)
- **Database name:** `taskdb`
- **Collection:** `tasks`

MongoDB is a **NoSQL** database. Data is stored as **documents** (JSON-like objects) inside **collections**, and each document gets a unique **ObjectId** as its `_id`. **Mongoose** is used to define a schema and model that give the data structure and validation.

### Task schema

| Field                | Type   | Rules                                                       |
| -------------------- | ------ | ----------------------------------------------------------- |
| title                | String | Required, trimmed, minimum 3 characters                     |
| description          | String | Optional, trimmed                                           |
| status               | String | `pending`, `in-progress` or `completed` (default `pending`) |
| priority             | String | `low`, `medium` or `high` (default `medium`)                |
| createdAt, updatedAt | Date   | Added automatically (`timestamps: true`)                    |

## Project Structure

```
backend/
├── src/
│   ├── config/
│   │   └── db.js               # connects to MongoDB Atlas
│   ├── controllers/
│   │   └── taskController.js   # logic for each CRUD operation
│   ├── middleware/
│   │   └── errorHandler.js     # 404 and error handling
│   ├── models/
│   │   └── Task.js             # Mongoose schema and model
│   ├── routes/
│   │   └── taskRoutes.js       # maps URLs to controller functions
│   └── server.js               # app entry point
├── docs/                       # Postman collection and screenshots
├── .env                        # secrets (not committed)
├── .env.example                # template for .env
├── .gitignore
├── package.json
└── README.md
```

## Request Flow

```
Client (Postman)
   ↓
Route          (taskRoutes.js matches the URL and method)
   ↓
Middleware     (express.json() parses the body)
   ↓
Controller     (taskController.js runs the logic)
   ↓
Model          (Task.js, Mongoose validates the data)
   ↓
MongoDB Atlas  (stores or reads the document)
   ↓
Response       (JSON with the right status code)
   ↓
Client
```

## Error Handling and Validation

- Mongoose validation errors (missing title, short title, invalid status) return **400** with a clear message.
- Invalid MongoDB ids return **400** (`Invalid task ID`).
- A valid id that doesn't exist returns **404** (`Task not found`).
- Unknown routes return **404**.
- Unexpected errors return **500** through a central error handler middleware.

## Security

- Secrets are stored in `.env` and never hardcoded.
- `.env` is in `.gitignore`.
- `.env.example` contains placeholders only.

## Testing

All endpoints were tested in Postman for both successful and failing requests.

- Postman collection: [`docs/Tasks-API.postman_collection.json`](docs/Tasks-API.postman_collection.json)
- Screenshots are in the [`docs`](docs) folder:
  - [01-create-task-201.png](docs/01-create-task-201.png)
  - [02-get-all-200.png](docs/02-get-all-200.png)
  - [03-get-one-200.png](docs/03-get-one-200.png)
  - [04-update-200.png](docs/04-update-200.png)
  - [05-missing-title-400.png](docs/05-missing-title-400.png)
  - [06-invalid-id-400.png](docs/06-invalid-id-400.png)
  - [07-not-found-404.png](docs/07-not-found-404.png)
  - [08-delete-200.png](docs/08-delete-200.png)
  - [09-atlas-cluster.png](docs/09-atlas-cluster.png)
  - [10-atlas-tasks-collection.png](docs/10-atlas-tasks-collection.png)

### Test cases covered

| Test                                        | Result |
| ------------------------------------------- | ------ |
| Create valid task                           | 201    |
| Create without title                        | 400    |
| Create with title shorter than 3 characters | 400    |
| Get all tasks, and filter by status         | 200    |
| Get one task by valid id                    | 200    |
| Get task with invalid id (`123`)            | 400    |
| Get task with non-existent id               | 404    |
| Update task                                 | 200    |
| Update with invalid status                  | 400    |
| Delete task                                 | 200    |
| Delete the same task again                  | 404    |
| Unknown route                               | 404    |

## Learning Summary

## Learning Summary

In Week 3 I moved from using APIs to building my own. I learned that Node.js lets JavaScript run on the server, and that Express makes it easy to create routes and handle requests and responses. I built a Task Manager REST API with full CRUD (create, read, update, delete) and organised the code into separate folders: routes, controllers, models, config and middleware, so each part has one job. I connected the app to MongoDB Atlas using Mongoose, and defined a schema with validation rules such as a required title and allowed status values. I also learned how middleware like `express.json()` and a central error handler work, and how to return proper HTTP status codes (200, 201, 400, 404). I kept my connection string in a `.env` file that is ignored by Git, so no secrets are uploaded. Finally, I tested every endpoint in Postman, including failing cases like missing fields and invalid ids.

## Blockers and Issues

1. **Postman web could not reach localhost:** the browser version of Postman uses a cloud agent that cannot access a local server. I fixed it by installing the Postman desktop app.
2. **`.gitignore` not applying at first:** `.env` and `node_modules` appeared in Source Control. I retyped `.gitignore`, saved it and refreshed, and they disappeared.
3. **Data saved in the wrong database:** my connection string had no database name, so documents went into the default `test` database. I added `/taskdb` to the URL in `.env` to fix it.
