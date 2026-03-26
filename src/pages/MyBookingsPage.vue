<script setup>
import { onBeforeMount, reactive, ref } from "vue";
import { useRouter } from "vue-router";
import { Notyf } from "notyf";
import api from "../api";
import { useGlobalStore } from "../stores/global";

const { user } = useGlobalStore();
const router = useRouter();
const notyf = new Notyf();

const state = reactive({
  loading: false,
  enrollments: [],
});
const cancellingId = ref("");

async function fetchEnrollments() {
  state.loading = true;
  try {
    const { data } = await api.get("/enrollments/get-enrollments");
    const enrollmentRows = Array.isArray(data) ? data : [];

    const enriched = await Promise.all(
      enrollmentRows.map(async (row) => {
        const firstCourseId = row.enrolledCourses?.[0]?.courseId;
        let course = null;

        if (firstCourseId) {
          try {
            const result = await api.get(`/courses/specific/${firstCourseId}`);
            course = result.data;
          } catch {
            course = null;
          }
        }

        return {
          ...row,
          course,
        };
      })
    );

    state.enrollments = enriched;
  } catch (e) {
    console.error(e);
    notyf.error(e.response?.data?.message || "Failed to load bookings.");
    state.enrollments = [];
  } finally {
    state.loading = false;
  }
}

async function cancelEnrollment(enrollmentId) {
  if (!enrollmentId || cancellingId.value) return;
  cancellingId.value = enrollmentId;
  try {
    const { data } = await api.patch(`/enrollments/${enrollmentId}/cancel`);
    notyf.success(data?.message || "Enrollment cancelled.");
    await fetchEnrollments();
  } catch (e) {
    console.error(e);
    notyf.error(e.response?.data?.message || "Unable to cancel enrollment.");
  } finally {
    cancellingId.value = "";
  }
}

onBeforeMount(async () => {
  if (!user.token) {
    router.push({ name: "Login" });
    return;
  }
  await fetchEnrollments();
});
</script>

<template>
  <div class="container page-shell">
    <div class="d-flex flex-column flex-md-row align-items-md-center justify-content-between gap-2">
      <div>
        <h1 class="text-primary mb-1 section-title">My Bookings</h1>
        <p class="section-subtitle mb-0">Manage your enrolled courses.</p>
      </div>
      <button class="btn btn-outline-primary" @click="fetchEnrollments" :disabled="state.loading">
        {{ state.loading ? "Refreshing..." : "Refresh" }}
      </button>
    </div>

    <div v-if="state.loading" class="text-center my-5">
      <div class="spinner-grow"></div>
    </div>

    <div v-else class="mt-4">
      <div v-if="state.enrollments.length === 0" class="alert alert-info">
        No bookings yet. Browse available courses from <router-link to="/courses">Courses</router-link>.
      </div>

      <div v-else class="row g-3">
        <div class="col-12" v-for="enrollment in state.enrollments" :key="enrollment._id">
          <div class="card app-card">
            <div class="card-body">
              <div class="d-flex flex-column flex-md-row justify-content-between gap-3">
                <div>
                  <div class="d-flex align-items-center gap-2">
                    <h5 class="mb-0">
                      {{ enrollment.course?.name || "Course unavailable" }}
                    </h5>
                    <span
                      class="badge"
                      :class="enrollment.status === 'Enrolled' ? 'text-bg-success' : 'text-bg-secondary'"
                    >
                      {{ enrollment.status }}
                    </span>
                  </div>

                  <p class="text-muted mb-1">
                    {{ enrollment.course?.description || "Course details are currently unavailable." }}
                  </p>
                  <small class="text-muted">
                    Total: PHP {{ enrollment.totalPrice }} | Booked: {{ enrollment.enrolledOn }}
                  </small>
                </div>

                <div class="d-flex align-items-start gap-2">
                  <router-link
                    v-if="enrollment.course?._id"
                    class="btn btn-outline-primary"
                    :to="{ path: `/courses/${enrollment.course._id}` }"
                  >
                    View Course
                  </router-link>

                  <button
                    v-if="enrollment.status === 'Enrolled'"
                    class="btn btn-outline-danger"
                    :disabled="cancellingId === enrollment._id"
                    @click="cancelEnrollment(enrollment._id)"
                  >
                    {{ cancellingId === enrollment._id ? "Cancelling..." : "Cancel Booking" }}
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

