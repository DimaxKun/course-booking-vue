# Course Booking App

Vue course booking app frontend that connects to your existing `course-api` backend.

## Features
- Users can register, login, update profile, and reset password
- Users can browse/search courses and enroll in available courses
- Admins can add, edit, archive, and activate courses
- Improved error handling and loading states across core flows

## Local setup

### 1) Install frontend dependencies

```sh
npm install
```

### 2) Environment variables

- Set `VITE_COURSE_BOOKING_API` in `.env` to your running `course-api` base URL.
- Example:
  - `VITE_COURSE_BOOKING_API=http://localhost:4000`

### 3) Run frontend

```sh
npm run dev
```

