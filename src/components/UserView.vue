<template>
  <div class="row">
      <div class="col mb-4">
          <h2 class="text-center text-primary py-1 section-title">Courses Offered</h2>
          <p class="text-center section-subtitle">Select a course to enroll in.</p>
    <!-- <CourseSearch /> -->
      </div>
  </div>
  <div class="row g-4">
        <CourseComponent v-for="course in coursesData" :courseData="course" />

  </div>
</template>

<script setup>
import CourseComponent from '../components/CourseComponent.vue';
// import CourseSearch from '../components/CourseSearch.vue';
import { defineProps } from 'vue';

const props = defineProps({
  coursesData: Array
});

</script>