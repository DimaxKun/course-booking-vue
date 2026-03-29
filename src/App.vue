<script>
  
  import NavbarComponent from './components/NavbarComponent.vue';
  import { useGlobalStore } from "./stores/global";
  import { onBeforeMount } from 'vue';

  export default {
    components: {
      NavbarComponent
    },
    setup(){

      const { getUserDetails } = useGlobalStore();

      onBeforeMount(()=>getUserDetails(localStorage.getItem("token")))
      
    }
  }
</script>

<template>
  <NavbarComponent />

  <router-view v-slot="{ Component }">
    <Transition name="fade" mode="out-in">
      <component :is="Component" />
    </Transition>
  </router-view>
</template>


<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
