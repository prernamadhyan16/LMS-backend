```markdown
# LMS API Documentation

This document provides detailed specifications for the Learning Management System (LMS) backend API.

**Base URL**: `http://localhost:5000`

---

## Authentication

Protected endpoints require a JSON Web Token (JWT) to be sent in the request header.

- **Header**: `Authorization`
- **Value**: `Bearer `

A token can be obtained by using the `/api/auth/login` endpoint.

---

## 1. Auth Endpoints

### **`POST /api/auth/signup`**

Registers a new user in the system. Can also be used to create an admin user by including `"role": "admin"`.

**Headers**
| Key            | Value              |
| :------------- | :----------------- |
| `Content-Type` | `application/json` |

**Request Body**
```
{
  "username": "testuser",
  "email": "test@example.com",
  "password": "password123",
  "role": "user"
}
```

**Responses**
- **`201 Created`** (Success)
  ```
  {
      "success": true,
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
  ```
- **`400 Bad Request`** (User already exists)
  ```
  {
      "success": false,
      "message": "User already exists"
  }
  ```

### **`POST /api/auth/login`**

Authenticates an existing user and returns a JWT for accessing protected routes.

**Headers**
| Key            | Value              |
| :------------- | :----------------- |
| `Content-Type` | `application/json` |

**Request Body**
```
{
  "email": "test@example.com",
  "password": "password123"
}
```

**Responses**
- **`200 OK`** (Success)
  ```
  {
      "success": true,
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
  ```
- **`401 Unauthorized`** (Invalid credentials)
  ```
  {
      "success": false,
      "message": "Invalid credentials"
  }
  ```

---

## 2. Course Endpoints

### **`POST /api/courses`**

Creates a new course. **(Admin only)**

**Headers**
| Key             | Value                       |
| :-------------- | :-------------------------- |
| `Content-Type`  | `application/json`          |
| `Authorization` | `Bearer `  |

**Request Body**
```
{
    "title": "Introduction to Python",
    "description": "A comprehensive course for Python beginners.",
    "instructor": "Jane Doe",
    "price": 99.99,
    "lessons": [
        {
            "title": "Lesson 1: Setup and Hello World",
            "videoUrl": "https://example.com/video1"
        }
    ]
}
```

**Responses**
- **`201 Created`** (Success)
  ```
  {
      "success": true,
      "data": {
          "_id": "60d5f1b3e6c9a4001f7b8e19",
          "title": "Introduction to Python",
          "instructor": "Jane Doe",
          "price": 99.99,
          "lessons": [ /* ... */ ]
      }
  }
  ```
- **`403 Forbidden`** (Non-admin user attempts access)
  ```
  {
      "success": false,
      "message": "Not authorized as an admin"
  }
  ```

### **`GET /api/courses`**

Retrieves a list of all publicly available courses.

**Responses**
- **`200 OK`** (Success)
  ```
  {
      "success": true,
      "data": [
          {
              "_id": "60d5f1b3e6c9a4001f7b8e19",
              "title": "Introduction to Python",
              "instructor": "Jane Doe"
          }
      ]
  }
  ```

### **`POST /api/courses/:id/enroll`**

Enrolls the currently authenticated user in a specific course.

**Path Parameters**
| Parameter | Type   | Description             |
| :-------- | :----- | :---------------------- |
| `id`      | String | The ID of the course to enroll in. |

**Headers**
| Key             | Value                     |
| :-------------- | :------------------------ |
| `Authorization` | `Bearer ` |

**Responses**
- **`201 Created`** (Success)
  ```
  {
      "success": true,
      "data": {
          "_id": "60d5f1c4e6c9a4001f7b8e1b",
          "user": "user_id_here",
          "course": "course_id_here",
          "enrolledAt": "2025-07-20T12:00:00.000Z"
      }
  }
  ```
- **`400 Bad Request`** (User already enrolled)
  ```
  {
      "success": false,
      "message": "User already enrolled"
  }
  ```

---

## 3. Progress Endpoints

### **`POST /api/progress/lessons/complete`**

Marks a lesson as completed for the authenticated user.

**Headers**
| Key             | Value                       |
| :-------------- | :-------------------------- |
| `Content-Type`  | `application/json`          |
| `Authorization` | `Bearer `   |

**Request Body**
```
{
    "courseId": "60d5f1b3e6c9a4001f7b8e19",
    "lessonId": "60d5f1b3e6c9a4001f7b8e1a"
}
```

**Responses**
- **`200 OK`** (Success)
  ```
  {
      "success": true,
      "data": {
          "completedLessons": ["60d5f1b3e6c9a4001f7b8e1a"],
          "progress": 50,
          /* ... other enrollment details */
      }
  }
  ```

### **`POST /api/progress/quizzes/attempt`**

Submits a user's answers for a quiz and records the score.

**Headers**
| Key             | Value                       |
| :-------------- | :-------------------------- |
| `Content-Type`  | `application/json`          |
| `Authorization` | `Bearer `   |

**Request Body**
```
{
    "courseId": "60d5f1b3e6c9a4001f7b8e19",
    "quizId": "60d5f1b3e6c9a4001f7b8e1c",
    "answers": [1][2]
}
```

**Responses**
- **`200 OK`** (Success)
  ```
  {
      "success": true,
      "data": {
          "score": 2,
          "total": 3
      }
  }
  ```

### **`GET /api/progress/:courseId`**

Retrieves the detailed progress for a user in a specific course.

**Path Parameters**
| Parameter | Type   | Description                      |
| :-------- | :----- | :------------------------------- |
| `courseId`| String | The ID of the course to get progress for. |

**Headers**
| Key             | Value                     |
| :-------------- | :------------------------ |
| `Authorization` | `Bearer ` |

**Responses**
- **`200 OK`** (Success)
  ```
  {
      "success": true,
      "data": {
          "course": "course_id_here",
          "user": "user_id_here",
          "progress": 50,
          "completedLessons": ["lesson_id_1"],
          "quizAttempts": [ /* ... */ ]
      }
  }
  ```
- **`404 Not Found`** (User is not enrolled)
  ```
  {
      "success": false,
      "message": "Not enrolled in this course"
  }
  ```
```
