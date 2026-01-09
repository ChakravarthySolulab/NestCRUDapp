# Setup and Testing Guide

Complete guide for setting up and testing the NestCRUD Task Management API.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [MongoDB Setup](#mongodb-setup)
3. [Project Setup](#project-setup)
4. [Running the Application](#running-the-application)
5. [Postman Testing Guide](#postman-testing-guide)
6. [API Reference](#api-reference)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you begin, ensure you have the following installed:

| Software | Version | Download Link |
|----------|---------|---------------|
| Node.js | v18 or higher | [nodejs.org](https://nodejs.org/) |
| MongoDB | v6 or higher | [mongodb.com](https://www.mongodb.com/try/download/community) |
| Postman | Latest | [postman.com](https://www.postman.com/downloads/) |

---

## MongoDB Setup

### Option 1: Local MongoDB (Windows)

1. **Check if MongoDB service is running:**
   ```cmd
   sc query MongoDB
   ```

2. **Start MongoDB service:**
   - Open **Services** (Win + R, type `services.msc`)
   - Find **MongoDB** in the list
   - Right-click and select **Start**

   Or run as Administrator:
   ```cmd
   net start MongoDB
   ```

3. **Verify connection using MongoDB Compass:**
   - Open MongoDB Compass
   - Connect to: `mongodb://localhost:27017`

### Option 2: MongoDB Atlas (Cloud)

1. Create a free account at [MongoDB Atlas](https://www.mongodb.com/atlas)
2. Create a new cluster
3. Get your connection string
4. Update `.env` file:
   ```env
   MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/nestcrud
   ```

### Option 3: Docker

```bash
docker run -d -p 27017:27017 --name mongodb mongo:latest
```

---

## Project Setup

### Step 1: Install Dependencies

```bash
npm install
```

### Step 2: Configure Environment

The `.env` file should contain:

```env
# MongoDB Connection
MONGODB_URI=mongodb://localhost:27017/nestcrud

# Application Port
PORT=3000
```

### Step 3: Verify Configuration

Check that `src/app.module.ts` uses ConfigModule for database connection.

---

## Running the Application

### Development Mode (Recommended for Testing)

```bash
npm run start:dev
```

Expected output:
```
[Nest] LOG [NestFactory] Starting Nest application...
[Nest] LOG [InstanceLoader] MongooseModule dependencies initialized
[Nest] LOG [InstanceLoader] TasksModule dependencies initialized
[Nest] LOG [RoutesResolver] AppController {/}
[Nest] LOG [RoutesResolver] TasksController {/tasks}
[Nest] LOG [NestApplication] Nest application successfully started
Listening on port 3000
```

### Production Mode

```bash
npm run build
npm run start:prod
```

---

## Postman Testing Guide

### Base URL

```
http://localhost:3000
```

---

### 1. Health Check

Verify the API is running.

| Property | Value |
|----------|-------|
| Method | `GET` |
| URL | `http://localhost:3000/` |
| Headers | None |
| Body | None |

**Expected Response:**
```
Hello World!
```

---

### 2. Create Task

Create a new task with all fields.

| Property | Value |
|----------|-------|
| Method | `POST` |
| URL | `http://localhost:3000/tasks` |
| Headers | `Content-Type: application/json` |

**Request Body:**
```json
{
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guide",
  "status": "in-progress",
  "priority": "high",
  "dueDate": "2026-01-15T00:00:00.000Z"
}
```

**Expected Response (201 Created):**
```json
{
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guide",
  "status": "in-progress",
  "priority": "high",
  "dueDate": "2026-01-15T00:00:00.000Z",
  "_id": "6781234567890abcdef12345",
  "createdAt": "2026-01-09T10:00:00.000Z",
  "updatedAt": "2026-01-09T10:00:00.000Z",
  "__v": 0
}
```

**Screenshot:**

![Create Task](./ss/CreateTask.png)

---

### 3. Create Task (Minimal)

Create a task with only the required field.

| Property | Value |
|----------|-------|
| Method | `POST` |
| URL | `http://localhost:3000/tasks` |
| Headers | `Content-Type: application/json` |

**Request Body:**
```json
{
  "title": "Quick task"
}
```

**Expected Response (201 Created):**
```json
{
  "title": "Quick task",
  "status": "pending",
  "priority": "medium",
  "_id": "6781234567890abcdef12346",
  "createdAt": "2026-01-09T10:05:00.000Z",
  "updatedAt": "2026-01-09T10:05:00.000Z",
  "__v": 0
}
```

---

### 4. Get All Tasks

Retrieve all tasks from the database.

| Property | Value |
|----------|-------|
| Method | `GET` |
| URL | `http://localhost:3000/tasks` |
| Headers | None |
| Body | None |

**Expected Response (200 OK):**
```json
[
  {
    "_id": "6781234567890abcdef12345",
    "title": "Complete project documentation",
    "description": "Write comprehensive README and setup guide",
    "status": "in-progress",
    "priority": "high",
    "dueDate": "2026-01-15T00:00:00.000Z",
    "createdAt": "2026-01-09T10:00:00.000Z",
    "updatedAt": "2026-01-09T10:00:00.000Z",
    "__v": 0
  },
  {
    "_id": "6781234567890abcdef12346",
    "title": "Quick task",
    "status": "pending",
    "priority": "medium",
    "createdAt": "2026-01-09T10:05:00.000Z",
    "updatedAt": "2026-01-09T10:05:00.000Z",
    "__v": 0
  }
]
```

**Screenshot:**

![Get All Tasks](./ss/GetallTasks.png)

---

### 5. Get Task by ID

Retrieve a specific task by its ID.

| Property | Value |
|----------|-------|
| Method | `GET` |
| URL | `http://localhost:3000/tasks/:id` |
| Headers | None |
| Body | None |

**Example URL:**
```
http://localhost:3000/tasks/6781234567890abcdef12345
```

**Expected Response (200 OK):**
```json
{
  "_id": "6781234567890abcdef12345",
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guide",
  "status": "in-progress",
  "priority": "high",
  "dueDate": "2026-01-15T00:00:00.000Z",
  "createdAt": "2026-01-09T10:00:00.000Z",
  "updatedAt": "2026-01-09T10:00:00.000Z",
  "__v": 0
}
```

**Error Response (404 Not Found):**
```json
{
  "statusCode": 404,
  "message": "Task with ID \"invalid-id\" not found",
  "error": "Not Found"
}
```

**Screenshot:**

![Get Task by ID](./ss/GetTaskByID.png)

---

### 6. Update Task

Update an existing task (partial update supported).

| Property | Value |
|----------|-------|
| Method | `PATCH` |
| URL | `http://localhost:3000/tasks/:id` |
| Headers | `Content-Type: application/json` |

**Example URL:**
```
http://localhost:3000/tasks/6781234567890abcdef12345
```

**Request Body (update status and priority):**
```json
{
  "status": "completed",
  "priority": "low"
}
```

**Expected Response (200 OK):**
```json
{
  "_id": "6781234567890abcdef12345",
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guide",
  "status": "completed",
  "priority": "low",
  "dueDate": "2026-01-15T00:00:00.000Z",
  "createdAt": "2026-01-09T10:00:00.000Z",
  "updatedAt": "2026-01-09T10:30:00.000Z",
  "__v": 0
}
```

**Screenshot:**

![Update Task](./ss/Update.png)

---

### 7. Delete Task

Delete a task by its ID.

| Property | Value |
|----------|-------|
| Method | `DELETE` |
| URL | `http://localhost:3000/tasks/:id` |
| Headers | None |
| Body | None |

**Example URL:**
```
http://localhost:3000/tasks/6781234567890abcdef12345
```

**Expected Response (200 OK):**
```json
{
  "_id": "6781234567890abcdef12345",
  "title": "Complete project documentation",
  "description": "Write comprehensive README and setup guide",
  "status": "completed",
  "priority": "low",
  "dueDate": "2026-01-15T00:00:00.000Z",
  "createdAt": "2026-01-09T10:00:00.000Z",
  "updatedAt": "2026-01-09T10:30:00.000Z",
  "__v": 0
}
```

**Screenshot:**

![Delete Task](./ss/Delete.png)

---

## API Reference

### Task Object

| Field | Type | Description |
|-------|------|-------------|
| `_id` | ObjectId | Unique identifier (auto-generated) |
| `title` | String | Task title (required) |
| `description` | String | Task description (optional) |
| `status` | Enum | Task status: `pending`, `in-progress`, `completed` |
| `priority` | Enum | Task priority: `low`, `medium`, `high` |
| `dueDate` | Date | Due date in ISO format (optional) |
| `createdAt` | Date | Creation timestamp (auto-generated) |
| `updatedAt` | Date | Last update timestamp (auto-generated) |

### HTTP Status Codes

| Code | Description |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request (validation error) |
| 404 | Not Found |
| 500 | Internal Server Error |

### Validation Rules

| Field | Rules |
|-------|-------|
| `title` | Required, must be a string |
| `description` | Optional, must be a string |
| `status` | Must be one of: `pending`, `in-progress`, `completed` |
| `priority` | Must be one of: `low`, `medium`, `high` |
| `dueDate` | Must be a valid ISO date string |

---

## Troubleshooting

### MongoDB Connection Error

**Error:**
```
MongooseServerSelectionError: connect ECONNREFUSED 127.0.0.1:27017
```

**Solution:**
1. Ensure MongoDB service is running
2. Check if MongoDB is installed correctly
3. Verify the connection string in `.env`

### Validation Errors

**Error:**
```json
{
  "statusCode": 400,
  "message": ["title must be a string", "title should not be empty"],
  "error": "Bad Request"
}
```

**Solution:**
- Ensure `Content-Type: application/json` header is set
- Verify request body contains required fields
- Check field values match expected types

### Port Already in Use

**Error:**
```
Error: listen EADDRINUSE: address already in use :::3000
```

**Solution:**
1. Find and kill the process using port 3000:
   ```cmd
   netstat -ano | findstr :3000
   taskkill /PID <PID> /F
   ```
2. Or change the port in `.env`:
   ```env
   PORT=3001
   ```

### Invalid ObjectId

**Error:**
```json
{
  "statusCode": 500,
  "message": "Internal server error"
}
```

**Solution:**
- Ensure the task ID is a valid 24-character MongoDB ObjectId
- Example valid ID: `6781234567890abcdef12345`

---

## Testing Checklist

Use this checklist to verify all endpoints work correctly:

- [ ] Health check returns "Hello World!"
- [ ] Create task with all fields
- [ ] Create task with only title
- [ ] Get all tasks returns array
- [ ] Get task by valid ID
- [ ] Get task by invalid ID returns 404
- [ ] Update task status
- [ ] Update task priority
- [ ] Update multiple fields
- [ ] Delete task by ID
- [ ] Delete already deleted task returns 404
- [ ] Validation error for missing title
- [ ] Validation error for invalid status
- [ ] Validation error for invalid priority

---

## Support

For issues or questions, please check the [README.md](./README.md) or create an issue in the repository.
