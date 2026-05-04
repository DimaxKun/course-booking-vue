<template>
  <div class="row g-4">
    <CourseComponent v-for="course in coursesData" :key="course._id" :courseData="course" />
    <div v-if="coursesData.length === 0" class="col-12">
      <div class="text-center py-5 text-muted">
        <i class="bi bi-journal-x" style="font-size: 2.5rem;"></i>
        <p class="mt-3">No courses found.</p>
      </div>
    </div>
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