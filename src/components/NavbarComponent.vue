<script setup>
  import { useGlobalStore } from "../stores/global";
  import { useRouter } from "vue-router";

  const { user } = useGlobalStore();
  const router = useRouter();

  router.afterEach(() => {
    const el = document.getElementById("navbarNavAltMarkup");
    if (el && el.classList.contains("show")) {
      el.classList.remove("show");
    }
  });
</script>

<template>
  <nav class="navbar navbar-expand-lg sticky-top bg-white shadow-sm">
    <div class="container">
      <router-link :to="{ name: 'Home' }" class="navbar-brand d-flex align-items-center gap-2">
        <span class="brand-icon"><i class="bi bi-mortarboard-fill"></i></span>
        <span class="fw-bold text-dark">CourseBooking</span>
      </router-link>

      <button class="navbar-toggler border-0" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup"
        aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarNavAltMarkup">
        <div class="navbar-nav ms-auto align-items-lg-center gap-1">
          <router-link :to="{ name: 'Courses' }" class="nav-link">Courses</router-link>
          <router-link :to="{ name: 'News' }" class="nav-link">News</router-link>
          <router-link :to="{ name: 'Profile' }" class="nav-link" v-if="user.email">Profile</router-link>
          <router-link :to="{ name: 'MyBookings' }" class="nav-link" v-if="user.email && !user.isAdmin">My Bookings</router-link>
          <router-link :to="{ name: 'AddCourse' }" class="nav-link" v-if="user.email && user.isAdmin">Add Course</router-link>

          <template v-if="!user.email">
            <router-link :to="{ name: 'Register' }" class="btn btn-outline-primary btn-sm px-3 ms-1">Register</router-link>
            <router-link :to="{ name: 'Login' }" class="btn btn-primary btn-sm px-3 ms-1">Login</router-link>
          </template>
          <template v-else>
            <span class="nav-user-greeting d-none d-lg-inline ms-2">Hi, {{ user.firstName || user.email }}</span>
            <router-link :to="{ name: 'Logout' }" class="btn btn-outline-danger btn-sm px-3 ms-1">Logout</router-link>
          </template>
        </div>
      </div>
    </div>
  </nav>
</template>

<style scoped>
.brand-icon {
  width: 32px;
  height: 32px;
  background: linear-gradient(135deg, #2563eb, #3b82f6);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 1rem;
}

.nav-user-greeting {
  font-size: 0.85rem;
  color: var(--text-muted);
  font-weight: 500;
}
</style>