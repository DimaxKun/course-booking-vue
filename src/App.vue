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

  <footer class="site-footer">
    <div class="container text-center">
      <span>© 2026 Mohammad Ali Dimacaling. All rights reserved.</span>
    </div>
  </footer>
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

.site-footer {
  background: var(--surface);
  border-top: 1px solid var(--border-soft);
  padding: 1.2rem 0;
  font-size: 0.85rem;
  color: var(--text-muted);
  margin-top: auto;
}
</style>
