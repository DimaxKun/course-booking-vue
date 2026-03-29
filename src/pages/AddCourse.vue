<template>
  <div class="container page-shell" style="max-width: 560px">
    <div class="mb-4">
      <h1 class="section-title text-primary mb-1">Add New Course</h1>
      <p class="section-subtitle">Fill in the details to publish a new course.</p>
    </div>

    <div class="app-card p-4">
      <form @submit.prevent="handleSubmit">
        <div class="mb-3">
          <label for="courseNameInput" class="form-label">Course Name</label>
          <div class="input-icon-wrap">
            <i class="bi bi-journal-bookmark input-icon"></i>
            <input type="text" class="form-control ps-icon" id="courseNameInput" placeholder="e.g. Full Stack Web Development" v-model="name" />
          </div>
        </div>
        <div class="mb-3">
          <label for="courseDescription" class="form-label">Description</label>
          <textarea class="form-control" id="courseDescription" v-model="description" rows="4" placeholder="What will students learn?"></textarea>
        </div>
        <div class="mb-4">
          <label for="coursePrice" class="form-label">Price (PHP)</label>
          <div class="input-icon-wrap">
            <i class="bi bi-currency-exchange input-icon"></i>
            <input type="number" class="form-control ps-icon" id="coursePrice" v-model="price" min="0" />
          </div>
        </div>
        <div class="d-grid">
          <button type="submit" class="btn btn-primary btn-auth" :disabled="!isEnabled">
            <i class="bi bi-plus-circle me-2"></i>Add Course
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.input-icon-wrap {
  position: relative;
}
.input-icon {
  position: absolute;
  left: 0.85rem;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-muted);
  pointer-events: none;
}
.ps-icon {
  padding-left: 2.4rem;
}
.btn-auth {
  padding: 0.65rem;
  font-size: 1rem;
}
</style>

<script setup>
import { ref, onBeforeMount, watch } from "vue";
import { Notyf } from "notyf";
import { useRouter } from "vue-router";
import { useGlobalStore } from "../stores/global";
import api from "../api";

const notyf = new Notyf();
const router = useRouter();
const { user } = useGlobalStore();

const name = ref("");
const description = ref("");
const price = ref(0);
const isEnabled = ref(false);

async function handleSubmit() {
  const course = {
      name: name.value,
      description: description.value,
      price: price.value,
  };

  try {
      const response = await api.post("/courses", course);

      if (response.status === 201) {
          notyf.success(response.data.message);

          router.push({ path: "/courses" });
      } else {
          notyf.error(response.data.message);
      }
  } catch (error) {
      if (error.response.status === 409) {
          notyf.error(error.response.data.message);
      } else {
          console.error(error.response.data.message);
          notyf.error("Error adding course. Please contact administrator.");
      }
  }
}

watch([name, description, price], (currentValue, oldValue) => {
  if (currentValue.every((input) => input !== "")) {
      isEnabled.value = true;
  } else {
      isEnabled.value = false;
  }
});

onBeforeMount(() => {
  if (!user.token || !user.isAdmin) {
      router.push({ path: "/courses" });
  }
});
</script>
