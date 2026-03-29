<script>
  import BannerComponent from '../components/BannerComponent.vue';
  import HighlightsComponent from '../components/HighlightsComponent.vue';

  export default {
    components: {
      BannerComponent,
      HighlightsComponent
    },
    
    data(){
      return {
          bannerProps: {
            title: "Zuitt Course Booking System",
            tagline: "Opportunities for everyone, everywhere.",
            destination: "Courses",
            buttonLabel: "View Our Courses"
          }
      };
    }
  }
</script>

<template>
  <div>
    <div class="container-fluid">
      <BannerComponent :bannerProps=bannerProps />
      <HighlightsComponent />
    </div>
    <footer class="bg-primary">
      <div class="text-center p-3 text-light">
        © 2026 Mohammad Ali Dimacaling. All rights reserved.
      </div>
    </footer>
  </div>
</template>