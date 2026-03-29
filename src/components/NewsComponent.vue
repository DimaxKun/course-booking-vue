<template>
  <div class="col-md-4">
    <div class="app-card h-100 p-4 d-flex flex-column">
      <h5 class="fw-semibold mb-2">{{ newsData.name }}</h5>
      <p class="text-muted mb-3" style="font-size: 0.93rem; flex: 1;">{{ newsData.description }}</p>
      <div class="d-flex align-items-center justify-content-between mt-auto pt-3" style="border-top: 1px solid var(--border-soft);">
        <span class="text-muted" style="font-size: 0.85rem;">
          <i class="bi bi-heart-fill text-danger me-1"></i>{{ likes }} likes
        </span>
        <button
          class="btn btn-sm btn-outline-primary"
          :disabled="likes >= 10"
          @click="likeNews"
        >
          {{ likes >= 10 ? "Liked" : "Like" }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>

	import { ref } from "vue";


  	export default {
	    props: {
	      newsData: Object
	    },
	    setup({newsData}){

	      const likes = ref(0);

	      function likeNews(){
	        likes.value++;
	      }
	      
	      return {
	        newsData,
	        likes,
	        likeNews
	      }
	    }
    }
</script>