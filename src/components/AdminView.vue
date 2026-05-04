<template>
  <div class="dashboard-table-wrap">
    <table class="table dashboard-table mb-0">
      <thead>
        <tr>
          <th>Name</th>
          <th class="d-none d-md-table-cell">Description</th>
          <th>Price</th>
          <th>Status</th>
          <th class="text-end">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="course in coursesData" :key="course._id">
          <td class="fw-semibold">{{ course.name }}</td>
          <td class="text-muted d-none d-md-table-cell" style="font-size: 0.88rem; max-width: 260px;">
            {{ course.description.slice(0, 80) + (course.description.length > 80 ? '...' : '') }}
          </td>
          <td class="fw-semibold text-primary">PHP {{ course.price }}</td>
          <td>
            <span class="badge" :class="course.isActive ? 'text-bg-success' : 'text-bg-secondary'">
              {{ course.isActive ? 'Active' : 'Archived' }}
            </span>
          </td>
          <td class="text-end">
            <div class="d-flex gap-2 justify-content-end">
              <EditCourseButton :course="course" @status-updated="refetchCourses" />
              <ToggleCourseStatusButton :course="course" @status-updated="refetchCourses" />
            </div>
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
