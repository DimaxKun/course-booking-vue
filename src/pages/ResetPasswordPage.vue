<script setup>
import { ref, watch, onBeforeMount } from "vue";
import { useRoute, useRouter } from "vue-router";
import { Notyf } from "notyf";
import api from "../api.js";

// NOTE: Update this endpoint to match your Express route.
// Example: app.use("/users", usersRoutes) then PUT "/users/reset-password"
const RESET_PASSWORD_ENDPOINT = "/users/reset-password";

const route = useRoute();
const router = useRouter();
const notyf = new Notyf();

const token = ref("");
const newPassword = ref("");
const confirmPassword = ref("");
const isEnabled = ref(false);
const submitting = ref(false);

onBeforeMount(() => {
  // Prefer token from URL (typical reset flow), fallback to localStorage token.
  const queryToken = route.query.token ? String(route.query.token) : "";
  token.value = queryToken || localStorage.getItem("token") || "";
});

watch(
  [newPassword, confirmPassword],
  ([p1, p2]) => {
    const pass1 = (p1 || "").trim();
    const pass2 = (p2 || "").trim();
    isEnabled.value = pass1 !== "" && pass2 !== "" && pass1 === pass2;
  },
  { immediate: true }
);

async function handleSubmit(e) {
  e.preventDefault();
  if (!token.value) {
    notyf.error("Missing reset token. Please use the reset link provided.");
    return;
  }
  if (submitting.value) return;

  submitting.value = true;
  try {
    await api.put(
      RESET_PASSWORD_ENDPOINT,
      { newPassword: newPassword.value },
      {
        // Explicitly pass the token in the Authorization header (backend requirement).
        headers: { Authorization: `Bearer ${token.value}` },
      }
    );

    notyf.success("Password reset successful. Please login.");
    newPassword.value = "";
    confirmPassword.value = "";
    router.push({ name: "Login" });
  } catch (err) {
    const message =
      err.response?.data?.message || err.message || "Password reset failed.";
    notyf.error(message);
  } finally {
    submitting.value = false;
  }
}
</script>

<template>
  <div class="container my-5">
    <div class="row justify-content-center">
      <div class="col-md-8 col-lg-5">
        <div class="card shadow-sm">
          <div class="card-body p-4">
            <h1 class="h4 text-center text-primary mb-3">Reset Password</h1>
            <p class="text-muted text-center mb-4">
              Enter your new password below.
            </p>

            <div v-if="!token" class="alert alert-warning">
              Reset token not found. Please open the reset link sent to your email
              (it should include a <code>?token=...</code>).
            </div>

            <form @submit="handleSubmit">
              <div class="mb-3">
                <label for="newPasswordInput" class="form-label">New Password</label>
                <input
                  id="newPasswordInput"
                  type="password"
                  class="form-control"
                  v-model="newPassword"
                />
              </div>

              <div class="mb-3">
                <label for="confirmPasswordInput" class="form-label"
                  >Confirm New Password</label
                >
                <input
                  id="confirmPasswordInput"
                  type="password"
                  class="form-control"
                  v-model="confirmPassword"
                />
              </div>

              <div class="d-grid mt-4">
                <button
                  v-if="!isEnabled"
                  type="button"
                  class="btn btn-secondary"
                  disabled
                >
                  Reset Password
                </button>
                <button
                  v-else
                  type="submit"
                  class="btn btn-primary"
                  :disabled="submitting"
                >
                  {{ submitting ? "Resetting…" : "Reset Password" }}
                </button>
              </div>
            </form>

            <div class="text-center mt-4">
              <router-link to="/login" class="link-primary">Back to Login</router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
