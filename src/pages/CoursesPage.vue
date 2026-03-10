<script>
import api from "../api.js";
import { reactive, watch } from "vue";

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

    const fetchCourses = async () => {
      if (!user.isLoading) {
        const { data } = user.isAdmin
          ? await api.get("/courses/all")
          : await api.get("/courses");

        courses.data = data;
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
      fetchCourses,
      handleSearchUpdate,
    };
  },
};
</script>

<template>
  <div class="container">
    <p v-if="user.isLoading">Loading...</p>

    <!-- Search bar only for users -->
    <CourseSearch
      v-if="!user.isAdmin && !user.isLoading"
      @updateCourses="handleSearchUpdate"
    />

    <AdminView
      v-if="user.isAdmin && !user.isLoading"
      :coursesData="courses.data"
      :fetchCourses="fetchCourses"
    />

    <UserView
      v-if="!user.isAdmin && !user.isLoading"
      :coursesData="courses.data"
    />
  </div>
</template>
