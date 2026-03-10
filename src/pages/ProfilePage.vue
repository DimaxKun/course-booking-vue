<script setup>
    import { onBeforeMount, ref, watch, nextTick } from 'vue';
    import { useRouter } from 'vue-router';
    import { useGlobalStore } from '../stores/global.js';
    import { Notyf } from 'notyf';
    import api from '../api.js';
    import { Modal } from 'bootstrap';

    const {user} = useGlobalStore();

    const router = useRouter()
    const notyf = new Notyf();

    // NOTE: Update these endpoints if your backend uses different paths.
    const RESET_PASSWORD_ENDPOINT = "/users/reset-password";
    const UPDATE_PROFILE_ENDPOINT = "/users/profile";

    const newPassword = ref("");
    const confirmPassword = ref("");
    const isEnabled = ref(false);
    const submitting = ref(false);
    const resetModalRef = ref(null);

    const profileModalRef = ref(null);
    const firstName = ref("");
    const lastName = ref("");
    const mobileNo = ref("");
    const profileEnabled = ref(false);
    const profileSubmitting = ref(false);

    onBeforeMount(() => {
        if(!user.email){
            router.push({path: '/'})
        }
    })

    watch([newPassword, confirmPassword], ([p1, p2]) => {
        const pass1 = (p1 || "").trim();
        const pass2 = (p2 || "").trim();
        isEnabled.value = pass1 !== "" && pass2 !== "" && pass1 === pass2;
    }, { immediate: true })

    watch([firstName, lastName, mobileNo], ([f, l, m]) => {
        const fTrimmed = (f || "").trim();
        const lTrimmed = (l || "").trim();
        const mTrimmed = (m || "").trim();
        profileEnabled.value = fTrimmed !== "" && lTrimmed !== "" && mTrimmed !== "";
    }, { immediate: true })

    function openResetModal() {
        // clear fields each time the modal opens
        newPassword.value = "";
        confirmPassword.value = "";

        nextTick(() => {
            if (resetModalRef.value) {
                Modal.getOrCreateInstance(resetModalRef.value).show();
            }
        });
    }

    function closeResetModal() {
        if (resetModalRef.value) {
            Modal.getOrCreateInstance(resetModalRef.value).hide();
        }
    }

    function openProfileModal() {
        // clear fields each time the modal opens
        firstName.value = "";
        lastName.value = "";
        mobileNo.value = "";

        nextTick(() => {
            if (profileModalRef.value) {
                Modal.getOrCreateInstance(profileModalRef.value).show();
            }
        });
    }

    function closeProfileModal() {
        if (profileModalRef.value) {
            Modal.getOrCreateInstance(profileModalRef.value).hide();
        }
    }

    async function handleResetPassword(e){
        e.preventDefault();

        const token = localStorage.getItem("token");
        if(!token){
            notyf.error("You must be logged in to reset your password.");
            return;
        }
        if(!isEnabled.value || submitting.value) return;

        submitting.value = true;
        try{
            // Backend expects: Authorization header + { newPassword } in the body
            await api.put(RESET_PASSWORD_ENDPOINT, 
                { newPassword: newPassword.value },
                { headers: { Authorization: `Bearer ${token}` } }
            );

            notyf.success("Password reset successful.");
            newPassword.value = "";
            confirmPassword.value = "";
            closeResetModal();
        }catch(err){
            const message = err.response?.data?.message || err.message || "Password reset failed.";
            notyf.error(message);
        }finally{
            submitting.value = false;
        }
    }

    async function handleUpdateProfile(e){
        e.preventDefault();

        const token = localStorage.getItem("token");
        if(!token){
            notyf.error("You must be logged in to update your profile.");
            return;
        }
        if(!profileEnabled.value || profileSubmitting.value) return;

        profileSubmitting.value = true;
        try{
            await api.put(UPDATE_PROFILE_ENDPOINT,
                {
                    firstName: firstName.value,
                    lastName: lastName.value,
                    mobileNo: mobileNo.value
                },
                { headers: { Authorization: `Bearer ${token}` } }
            );

            notyf.success("Profile updated successfully.");
            closeProfileModal();
        }catch(err){
            const message = err.response?.data?.message || err.message || "Failed to update profile.";
            notyf.error(message);
        }finally{
            profileSubmitting.value = false;
        }
    }
</script>

<template>
    <div class="container-fluid" v-if="user.email">
        <h1 class="my-5 pt-3 text-primary text-center">Profile Page</h1> 
        <div class="row d-flex justify-content-center">
            <div class="col-md-5 border border rounded-3 mx-auto p-5">
                <h2 className="mt-3">Juan Dela Cruz</h2>
                <hr />
                <h4>Contacts</h4>
                <ul>
                    <li>Email: jdelacruz@mail.com</li>
                    <li>Mobile No: 09123456789</li>
                </ul>

                <hr />

                <h4 class="mt-4">Update Profile</h4>
                <div class="d-grid mt-3">
                    <button type="button" class="btn btn-outline-primary" @click="openProfileModal">
                        Update Profile
                    </button>
                </div>

                <h4 class="mt-4">Reset Password</h4>
                <div class="d-grid mt-3">
                    <button type="button" class="btn btn-primary" @click="openResetModal">
                        Reset Password
                    </button>
                </div>
            </div>
        </div>

        <!-- Update Profile Modal -->
        <div
            ref="profileModalRef"
            class="modal fade"
            tabindex="-1"
            aria-labelledby="updateProfileModalLabel"
            aria-hidden="true"
        >
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="updateProfileModalLabel">Update Profile</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>

                    <form v-on:submit="handleUpdateProfile">
                        <div class="modal-body">
                            <div class="mb-3">
                                <label for="firstNameInput" class="form-label">First Name</label>
                                <input type="text" class="form-control" id="firstNameInput" v-model="firstName" />
                            </div>
                            <div class="mb-3">
                                <label for="lastNameInput" class="form-label">Last Name</label>
                                <input type="text" class="form-control" id="lastNameInput" v-model="lastName" />
                            </div>
                            <div class="mb-3">
                                <label for="mobileNoInput" class="form-label">Mobile No</label>
                                <input type="text" class="form-control" id="mobileNoInput" v-model="mobileNo" />
                            </div>
                        </div>

                        <div class="modal-footer">
                            <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">
                                Cancel
                            </button>

                            <button type="button" class="btn btn-secondary" v-if="!profileEnabled" disabled>
                                Save Changes
                            </button>
                            <button type="submit" class="btn btn-primary" v-else :disabled="profileSubmitting">
                                {{ profileSubmitting ? "Saving..." : "Save Changes" }}
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <!-- Reset Password Modal -->
        <div
            ref="resetModalRef"
            class="modal fade"
            tabindex="-1"
            aria-labelledby="resetPasswordModalLabel"
            aria-hidden="true"
        >
            <div class="modal-dialog modal-dialog-centered">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="resetPasswordModalLabel">Reset Password</h5>
                        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                    </div>

                    <form v-on:submit="handleResetPassword">
                        <div class="modal-body">
                            <div class="mb-3">
                                <label for="newPasswordInput" class="form-label">New Password</label>
                                <input type="password" class="form-control" id="newPasswordInput" v-model="newPassword" />
                            </div>
                            <div class="mb-3">
                                <label for="confirmPasswordInput" class="form-label">Confirm New Password</label>
                                <input type="password" class="form-control" id="confirmPasswordInput" v-model="confirmPassword" />
                            </div>
                        </div>

                        <div class="modal-footer">
                            <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">
                                Cancel
                            </button>

                            <button type="button" class="btn btn-secondary" v-if="!isEnabled" disabled>
                                Reset Password
                            </button>
                            <button type="submit" class="btn btn-primary" v-else :disabled="submitting">
                                {{ submitting ? "Resetting..." : "Reset Password" }}
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>