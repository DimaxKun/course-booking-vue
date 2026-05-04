<script setup>
    import { computed, onBeforeMount, reactive, ref } from "vue";
    import { useRoute, useRouter } from "vue-router";
    import api from "../api";

    import { useGlobalStore } from "../stores/global";

    import { Notyf } from "notyf";

    const notyf = new Notyf();

    const { user } = useGlobalStore();

    const router = useRouter();

    const course = reactive({ data: null });
    const state = reactive({ loading: false, enrolling: false, error: "" });
    const route = useRoute();

    const canBook = computed(() => {
        if (!course.data) return false;
        if (!user.email || user.isAdmin) return false;
        if (course.data.isActive === false) return false;
        return true;
    });

    async function handleEnroll() {
        if (!canBook.value || state.enrolling) return;
        state.enrolling = true;
        try {
            const { data } = await api.post(`/enrollments/enroll`, {
                enrolledCourses: [{ courseId: course.data._id }],
                totalPrice: course.data.price,
            });

            if (data?.success === true) {
                notyf.success(data.message || "Enrollment successful.");
                router.push({ path: "/my-bookings" });
            } else {
                notyf.error(data?.message || "Enrollment failed.");
            }
        } catch (e) {
            console.error(e);
            notyf.error(e.response?.data?.message || "Booking failed. Please try again.");
        } finally {
            state.enrolling = false;
        }
    }

    onBeforeMount(async () => {
        state.loading = true;
        state.error = "";
        try {
            let { data } = await api.get(`/courses/specific/${route.params.id}`);
            course.data = data;
        } catch (e) {
            console.error(e);
            state.error = e.response?.data?.message || "Failed to load course.";
            notyf.error(state.error);
        } finally {
            state.loading = false;
        }
    });
</script>

<template>
    <div class="container page-shell">
        <nav aria-label="breadcrumb" class="mb-4">
            <ol class="breadcrumb">
                <li class="breadcrumb-item"><router-link to="/courses">Courses</router-link></li>
                <li class="breadcrumb-item active" aria-current="page">
                    {{ course.data ? course.data.name : "..." }}
                </li>
            </ol>
        </nav>

        <div class="text-center my-5" v-if="state.loading">
            <div class="spinner-grow text-primary"></div>
        </div>

        <div class="alert alert-danger" v-if="!state.loading && !course.data && state.error">
            {{ state.error }}
        </div>

        <div class="app-card overflow-hidden" v-if="course.data">
            <div class="row g-0">
                <div class="col-md-5">
                    <img
                        class="img-fluid w-100 h-100 course-detail-img"
                        :src="`https://placehold.co/600x400/2563eb/ffffff?font=lora&text=${encodeURIComponent(course.data.name)}`"
                    />
                </div>
                <div class="col-md-7 p-4 p-md-5 d-flex flex-column">
                    <div class="mb-2">
                        <span v-if="course.data.isActive === false" class="badge text-bg-warning mb-2">Unavailable</span>
                        <span v-else class="badge text-bg-success mb-2">Available</span>
                    </div>

                    <h1 class="section-title mb-2">{{ course.data.name }}</h1>
                    <p class="text-muted mb-4">{{ course.data.description }}</p>

                    <div class="course-price-tag mb-4">
                        <span class="price-label">Price</span>
                        <span class="price-value">PHP {{ course.data.price }}</span>
                    </div>

                    <div class="mt-auto">
                        <div v-if="course.data.isActive === false" class="alert alert-warning py-2">
                            This course is currently unavailable for booking.
                        </div>

                        <button
                            class="btn btn-primary btn-lg w-100"
                            type="button"
                            v-if="user.email && !user.isAdmin"
                            @click="handleEnroll"
                            :disabled="!canBook || state.enrolling"
                        >
                            <i class="bi bi-calendar-check me-2"></i>
                            {{ state.enrolling ? "Booking..." : "Book This Course" }}
                        </button>

                        <div v-if="user.email && user.isAdmin" class="alert alert-secondary py-2 mb-0 text-center">
                            <i class="bi bi-info-circle me-1"></i> Admins cannot enroll in courses.
                        </div>

                        <router-link to="/login" class="btn btn-outline-primary btn-lg w-100" v-if="!user.email">
                            <i class="bi bi-box-arrow-in-right me-2"></i>Login to Book
                        </router-link>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.course-detail-img {
  object-fit: cover;
  min-height: 280px;
}

.course-price-tag {
  display: flex;
  align-items: baseline;
  gap: 0.5rem;
}

.price-label {
  font-size: 0.85rem;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.price-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary);
}
</style>

