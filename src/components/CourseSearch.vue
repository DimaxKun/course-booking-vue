<script setup>
import { ref, watch } from "vue";
import { Notyf } from "notyf";
import api from "../api.js";

const emit = defineEmits(["updateCourses"]);

const searchTerm = ref("");
const searching = ref(false);
const notyf = new Notyf();
let debounceId = null;

const searchCourses = async () => {
  if (!searchTerm.value.trim()) {
    emit("updateCourses", null);
    return;
  }

  searching.value = true;
  try {
    const { data } = await api.post("/courses/search", {
      courseName: searchTerm.value,
    });
    emit("updateCourses", data);
  } catch (e) {
    console.error(e);
    notyf.error(e.response?.data?.message || "Search failed.");
  } finally {
    searching.value = false;
  }
};

watch(searchTerm, () => {
  if (debounceId) clearTimeout(debounceId);
  debounceId = setTimeout(() => {
    searchCourses();
  }, 250);
});
</script>

<template>
  <div class="row my-4 search-shell">
    <div class="col-lg-6 col-md-8 col-sm-10 mx-auto">
      <div class="input-group input-group-lg shadow-sm rounded-pill overflow-hidden">
        
        <span class="input-group-text bg-white border-0 ps-4">
          <i class="bi" :class="searching ? 'bi-hourglass-split text-muted' : 'bi-search text-muted'"></i>
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
