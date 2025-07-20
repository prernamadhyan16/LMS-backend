# Learning Management System API

A Node.js backend for managing online courses, user enrollments, and learning progress.

## Quick Start

```bash
npm install
npm run dev
```

Server runs on `http://localhost:5000`

## Setup

1. Create a `.env` file:
```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/lms-backend
JWT_SECRET=your-super-secret-jwt-key-here
```

2. Make sure MongoDB is running

## API Endpoints

### Authentication

**Register User**
```http
POST /api/auth/signup
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com", 
  "password": "password123",
  "role": "user"
}
```

**Login**
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

### Courses

**Get All Courses**
```http
GET /api/courses
```

**Create Course** (Admin only)
```http
POST /api/courses
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "JavaScript Basics",
  "description": "Learn JavaScript fundamentals",
  "instructor": "Sarah Wilson",
  "price": 49.99,
  "lessons": [
    {
      "title": "Variables and Data Types",
      "videoUrl": "https://example.com/video1"
    }
  ]
}
```

**Enroll in Course**
```http
POST /api/courses/:courseId/enroll
Authorization: Bearer <token>
```

### Progress Tracking

**Mark Lesson Complete**
```http
POST /api/progress/lessons/complete
Authorization: Bearer <token>
Content-Type: application/json

{
  "courseId": "course_id_here",
  "lessonId": "lesson_id_here"
}
```

**Get Progress**
```http
GET /api/progress/:courseId
Authorization: Bearer <token>
```

<<<<<<< HEAD
## Usage Examples

### Creating an Admin User
```bash
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin",
    "email": "admin@example.com",
    "password": "admin123",
    "role": "admin"
  }'
```

### Creating a Course
```bash
# First login to get token
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "admin123"
  }'

# Then create course with the token
curl -X POST http://localhost:5000/api/courses \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{
    "title": "React Fundamentals",
    "description": "Build modern web apps with React",
    "instructor": "Mike Johnson",
    "price": 79.99,
    "lessons": []
  }'
```

## Project Structure

```
=======
## Project Structure

```
>>>>>>> 387b646b2539573d4fb6285604c53541eb4d14fb
├── config/
│   └── db.js          # Database connection
├── controllers/
│   ├── authController.js
│   ├── courseController.js
│   └── progressController.js
├── middleware/
│   └── authMiddleware.js
├── models/
│   ├── Course.js
│   ├── Enrollment.js
│   └── User.js
├── routes/
│   ├── authRoutes.js
│   ├── courseRoutes.js
│   └── progressRoutes.js
└── server.js
```

## Features

- User authentication with JWT
- Role-based access control (user/admin)
- Course creation and management
- Student enrollment system
- Progress tracking
