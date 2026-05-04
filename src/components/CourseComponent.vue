<template>
  <div class="col-12 col-md-6 col-lg-4">
    <div class="course-card h-100 d-flex flex-column">
      <div class="course-card-img-wrap">
        <img
          class="course-card-img"
          :src="`https://placehold.co/600x400/2563eb/ffffff?font=lora&text=${encodeURIComponent(courseData.name)}`"
          :alt="courseData.name"
        />
      </div>
      <div class="course-card-body d-flex flex-column gap-2 p-4">
        <h5 class="fw-bold mb-1">{{ courseData.name }}</h5>
        <p class="text-muted mb-2" style="font-size: 0.9rem; flex: 1;">
          {{ courseData.description.slice(0, 100) + (courseData.description.length > 100 ? '...' : '') }}
        </p>
        <div class="d-flex align-items-center justify-content-between mt-auto pt-2" style="border-top: 1px solid var(--border-soft);">
          <span class="course-price">PHP {{ courseData.price }}</span>
          <router-link class="btn btn-primary btn-sm px-3" :to="{ path: `/courses/${courseData._id}` }">
            View <i class="bi bi-arrow-right ms-1"></i>
          </router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      courseData: Object
    },
    setup(){
      return {};
    }
  }
</script>

<style scoped>
.course-card-img-wrap {
  overflow: hidden;
  border-radius: var(--radius) var(--radius) 0 0;
}

.course-card-img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.course-card:hover .course-card-img {
  transform: scale(1.04);
}

.course-card-body {
  flex: 1;
}

.course-price {
  font-weight: 700;
  color: var(--primary);
  font-size: 1rem;
}
</style>

