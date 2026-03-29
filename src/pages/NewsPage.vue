<script setup>
	import { ref, watch, onBeforeMount } from 'vue';
	import { Notyf } from 'notyf';
	import { useGlobalStore } from '../stores/global.js';

	const {user} = useGlobalStore();

	const email = ref("");
	const feedback = ref("");
	const isEnabled = ref(false);
	const userEmail = ref("");

	const notyf = new Notyf();

	watch([email,feedback], (currentValue, oldValue) => {

        if(currentValue.every(input => input !== "")){
            isEnabled.value = true;
        } else {
            isEnabled.value = false;
        }
    });

    function handleSubmit(e){
        
        e.preventDefault();

        notyf.success("Feedback Sent");

        email.value = "";
        feedback.value = "";
    }

    onBeforeMount(()=>{ 
    	userEmail.value = localStorage.getItem("email")
  	});
</script>

<script>
	import newsData from '../data/newsData.js';

	import NewsComponent from '../components/NewsComponent.vue';

 	export default {
 		components: {
 			NewsComponent
 		}
 	}
</script>

<template>
  <div class="container page-shell">
    <div class="mb-5">
      <h1 class="section-title text-primary mb-1">Latest News</h1>
      <p class="section-subtitle">Stay up to date with what's happening.</p>
    </div>

    <div class="row g-4">
      <NewsComponent v-for="indivNews in newsData" :newsData="indivNews" />
    </div>

    <div v-if="user.email" class="mt-5">
      <div class="app-card p-4 p-md-5" style="max-width: 700px; margin: 0 auto;">
        <h4 class="fw-semibold mb-1"><i class="bi bi-chat-square-text-fill me-2 text-primary"></i>Send Feedback</h4>
        <p class="text-muted mb-4" style="font-size: 0.9rem;">We'd love to hear what you think.</p>
        <form @submit="handleSubmit">
          <div class="mb-3">
            <label for="emailInput" class="form-label">Email Address</label>
            <div class="input-icon-wrap">
              <i class="bi bi-envelope input-icon"></i>
              <input type="email" class="form-control ps-icon" id="emailInput" placeholder="you@example.com" v-model="email" />
            </div>
          </div>
          <div class="mb-4">
            <label for="feedbackInput" class="form-label">Your Feedback</label>
            <textarea class="form-control" id="feedbackInput" placeholder="Let us know what you think..." rows="4" v-model="feedback"></textarea>
          </div>
          <div class="d-grid">
            <button type="submit" class="btn btn-primary" :disabled="!isEnabled">
              <i class="bi bi-send me-2"></i>Send Feedback
            </button>
          </div>
        </form>
      </div>
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
</style>