<template>
    <div class="layout">
      <LoadingModal :show="$loading.loading"/>
      <router-view />
    </div>
  </template>
  
  <script>
import LoadingModal from '@/components/loadingModal.vue';

  export default {
    name: 'Layout',
    components: {
        LoadingModal
    },
    created() { 
      if(!this.$userStore.isLoggedIn){ 
        console.log('your not logged in ')
        this.$router.push('/login')
      }
      else if(this.$gameStore.empty){
        this.$router.push('/landing');
      }
    },

  }
  </script>
  
  <style>
  .layout {
    display: flex;
    flex-direction: column;
    min-height: 100vh; 
  }
  .footer {
    margin-top: auto; 
  }
  </style>
  