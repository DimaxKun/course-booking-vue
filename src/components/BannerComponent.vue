
<template>
  <div class="row mb-5 py-4 py-md-5" id="banner">
    <div class="col-10 col-md-8 p-4 p-md-5 ms-3 ms-md-5">
      <h1 class="mb-3" id="banner-title">{{bannerProps.title}}</h1>
      <p class="mb-4 mb-md-5" id="motto">{{bannerProps.tagline}}</p>
      <router-link class="btn btn-primary btn-lg rounded-pill px-3" :to="{ name: bannerProps.destination }">{{bannerProps.buttonLabel}} <i class="bi bi-arrow-right"></i></router-link>
    </div>
  </div>
</template>

<script>

export default {
  
  props: {
    
    bannerProps: Object
  },
  
  mounted(){
    console.log(this.bannerProps.title);
    console.log(this.bannerProps.tagline);
  }
}
</script>