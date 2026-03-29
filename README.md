<div align="center">

# 📚 Course Booking System

A full-stack web application for browsing, booking, and managing courses — built with Vue 3 and Node.js/Express.

![Vue](https://img.shields.io/badge/Vue_3-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![Bootstrap](https://img.shields.io/badge/Bootstrap_5-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)

</div>

---

## ✨ Features

### 👤 Users
- Register and log in with JWT-based authentication
- Browse and search available courses
- Book and cancel course enrollments
- View all personal bookings in one place
- Update profile info and reset password

### 🛠️ Admins
- Add new courses to the catalog
- Edit existing course details
- Archive or activate courses
- View all courses including inactive ones

---

## 🗂️ Project Structure

```
├── src/                    # Vue 3 frontend
│   ├── components/         # Reusable UI components
│   ├── pages/              # Route-level page components
│   ├── stores/             # Pinia global state
│   ├── assets/             # Global CSS
│   └── api.js              # Axios instance
│
└── course-api/             # Express backend
    ├── controllers/        # Route logic (user, course, enrollment)
    ├── models/             # Mongoose schemas
    ├── routes/             # Express routers
    ├── auth.js             # JWT middleware
    └── lambda.js           # AWS Lambda entry point
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js `^20.19.0` or `>=22.12.0`
- MongoDB instance (local or Atlas)

---

### 1. Clone the repository

```sh
git clone https://github.com/your-username/course-booking-system.git
cd course-booking-system
```

---

### 2. Set up the backend

```sh
cd course-api
npm install
```

Create a `.env` file inside `course-api/`:

```env
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET_KEY=your_jwt_secret
PORT=4000
```

Start the API server:

```sh
npm start
```

> The API will run on `http://localhost:4000`

---

### 3. Set up the frontend

```sh
cd ..
npm install
```

Create a `.env` file in the root:

```env
VITE_COURSE_BOOKING_API=http://localhost:4000
```

Start the dev server:

```sh
npm run dev
```

> The app will run on `http://localhost:5173`

---

## 🔌 API Overview

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| `POST` | `/users/register` | Register a new user | Public |
| `POST` | `/users/login` | Login and receive JWT | Public |
| `PUT` | `/users/profile` | Update user profile | User |
| `PUT` | `/users/reset-password` | Reset password | User |
| `GET` | `/courses` | Get all active courses | Public |
| `GET` | `/courses/all` | Get all courses | Admin |
| `GET` | `/courses/specific/:id` | Get a single course | Public |
| `POST` | `/courses` | Add a new course | Admin |
| `PUT` | `/courses/:id` | Update a course | Admin |
| `PATCH` | `/courses/:id/archive` | Archive a course | Admin |
| `GET` | `/enrollments/get-enrollments` | Get user enrollments | User |
| `POST` | `/enrollments/enroll` | Enroll in a course | User |
| `PATCH` | `/enrollments/:id/cancel` | Cancel an enrollment | User |

---

## 🧰 Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vue 3, Vue Router, Pinia |
| Styling | Bootstrap 5, Bootstrap Icons |
| HTTP Client | Axios |
| Notifications | Notyf |
| Build Tool | Vite |
| Backend | Node.js, Express 5 |
| Database | MongoDB, Mongoose |
| Auth | JSON Web Tokens (JWT), bcrypt |
| Deployment | AWS Lambda (Serverless Express) |

---

## 📦 Build for Production

```sh
npm run build
```

Output will be in the `dist/` folder, ready to be served statically.

---

## 👨‍💻 Author

**Mohammad Ali Dimacaling**

---

<div align="center">
  <sub>© 2026 Mohammad Ali Dimacaling. All rights reserved.</sub>
</div>
