<script>
import api from "../api.js";
import { reactive, watch } from "vue";
import { Notyf } from "notyf";

import CourseSearch from "../components/CourseSearch.vue";
import UserView from "../components/UserView.vue";
import AdminView from "../components/AdminView.vue";

import { useGlobalStore } from "../stores/global.js";

export default {
  components: {
    CourseSearch,
    UserView,
    AdminView,
  },

  setup() {
    const { user } = useGlobalStore();
    const courses = reactive({ data: [] });
    const state = reactive({ loading: false });
    const notyf = new Notyf();

    const fetchCourses = async () => {
      if (!user.isLoading) {
        state.loading = true;
        try {
          const { data } = user.isAdmin
            ? await api.get("/courses/all")
            : await api.get("/courses");

          courses.data = data;
        } catch (e) {
          console.error(e);
          notyf.error(e.response?.data?.message || "Failed to load courses.");
          courses.data = [];
        } finally {
          state.loading = false;
        }
      }
    };

    const handleSearchUpdate = (searchedCourses) => {
      if (searchedCourses === null) {
        fetchCourses(); // reset
      } else {
        courses.data = searchedCourses;
      }
    };

    watch([user], fetchCourses, { immediate: true });

    return {
      courses,
      user,
      state,
      fetchCourses,
      handleSearchUpdate,
    };
  },
};
</script>

<template>
  <div class="container page-shell">
    <div class="d-flex flex-column flex-md-row justify-content-between align-items-md-center mb-4 gap-2">
      <div>
        <h1 class="section-title text-primary mb-1">{{ user.isAdmin ? "Admin Course Management" : "Explore Courses" }}</h1>
        <p class="section-subtitle mb-0">
          {{ user.isAdmin ? "Manage course catalog, status, and updates." : "Browse available courses and book what fits your goals." }}
        </p>
      </div>
    </div>

    <div class="text-center py-5" v-if="user.isLoading || state.loading">
      <div class="spinner-grow text-primary"></div>
    </div>

    <!-- Search bar only for users -->
    <CourseSearch
      v-if="!user.isAdmin && !user.isLoading && !state.loading"
      @updateCourses="handleSearchUpdate"
    />

    <AdminView
      v-if="user.isAdmin && !user.isLoading && !state.loading"
      :coursesData="courses.data"
      :fetchCourses="fetchCourses"
    />

    <UserView
      v-if="!user.isAdmin && !user.isLoading && !state.loading"
      :coursesData="courses.data"
    />
  </div>
</template>
