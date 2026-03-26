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
    <div class="container">
        <nav class="my-3" aria-label="breadcrumb">
            <ol class="breadcrumb">
                <li class="breadcrumb-item"><router-link to="/courses">Courses</router-link></li>
               
                <li class="breadcrumb-item active" aria-current="page">
                    {{ course.data ? course.data.name : "..." }}
                </li>
            </ol>
        </nav>
        
        <div class="row mx-auto my-3 gap-4 gap-md-0" v-if="course.data">
            <div class="col-12 col-md-6">
                
                <img
                    class="img-fluid rounded"
                    :src="`https://placehold.co/600x400/377399/ffffff?font=lora&text=${encodeURIComponent(
                        course.data.name
                    )}`"
                />
            </div>
            <div class="col-12 col-md-6">
                <div class="d-flex gap-2 text-primary">
                    <h1 class="bi bi-mortarboard"></h1>
                    
                    <h1 class="mb-3">{{ course.data.name }}</h1>
                </div>
                <h6>Course Description:</h6>
                <p class="text-muted">
                    
                    {{ course.data.description }}
                </p>
               
                <p>Price: PHP {{ course.data.price }}</p>

                <div v-if="course.data.isActive === false" class="alert alert-warning">
                    This course is currently unavailable.
                </div>
                
                
                <button
                    class="btn btn-primary"
                    type="button"
                    v-if="user.email && !user.isAdmin"
                    @click="handleEnroll"
                    :disabled="!canBook || state.enrolling"
                >
                    {{ state.enrolling ? "Booking..." : "Book Course" }}
                </button>
                
                <button class="btn btn-danger" type="button" v-if="user.email && user.isAdmin" disabled>
                    Admin are not allowed to enroll
                </button>
                
                <router-link to="/login" class="btn btn-outline-danger" type="button" v-if="!user.email">
                    Login to Enroll
                </router-link>
                
            </div>
        </div>

        
        <div class="text-center my-5" v-if="state.loading">
            <div class="spinner-grow"></div>
        </div>

        <div class="alert alert-danger my-4" v-if="!state.loading && !course.data && state.error">
            {{ state.error }}
        </div>
    </div>
</template>

