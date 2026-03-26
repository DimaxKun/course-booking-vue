<template>
  <h2 class="text-center text-primary mb-4 section-title">Admin Dashboard</h2>
  <div class="dashboard-table-wrap">
  <table class="table table-striped dashboard-table">
      <thead>
          <tr>
              <th>ID</th>
              <th>Name</th>
              <th>Description</th>
              <th>Price</th>
              <th>Availability</th>
              <th colspan="2">Actions</th>
          </tr>
      </thead>
      <tbody>
          <tr v-for="course in coursesData" :key="course._id">
              <td>{{ course._id }}</td>
              <td>{{ course.name }}</td>
              <td>{{ course.description }}</td>
              <td>{{ course.price }}</td>
              <td>
                  <span v-if="course.isActive" class="text-success">Available</span>
                  <span v-else class="text-danger">Unavailable</span>
              </td>
              <td>
                  <EditCourseButton :course="course" @status-updated="refetchCourses" />
              </td>
              <td>
                  <ToggleCourseStatusButton :course="course" @status-updated="refetchCourses" />
              </td>
          </tr>
      </tbody>
  </table>
  </div>
</template>

<script setup>
import EditCourseButton from "./EditCourseButton.vue";
import ToggleCourseStatusButton from "./ToggleCourseStatusButton.vue";

const props = defineProps({
  coursesData: Array,
  fetchCourses: Function
});

const refetchCourses = () => {
  props.fetchCourses();
};

</script>
