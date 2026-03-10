<script setup>
import { ref, watch } from "vue";
import api from "../api.js";

const emit = defineEmits(["updateCourses"]);

const searchTerm = ref("");

const searchCourses = async () => {
  if (!searchTerm.value.trim()) {
    emit("updateCourses", null);
    return;
  }

  const { data } = await api.post("/courses/search", {
    courseName: searchTerm.value,
  });

  emit("updateCourses", data);
};

watch(searchTerm, searchCourses);
</script>

<template>
  <div class="row my-5">
    <div class="col-lg-6 col-md-8 col-sm-10 mx-auto">
      <div class="input-group input-group-lg shadow-sm rounded-pill overflow-hidden">
        
        <span class="input-group-text bg-white border-0 ps-4">
          <i class="bi bi-search text-muted"></i>
        </span>

        <input
          type="text"
          class="form-control border-0 py-3 px-3"
          placeholder="Search courses..."
          v-model="searchTerm"
        />

      </div>
    </div>
  </div>
</template>
