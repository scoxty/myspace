<template>
  <NarBar />
  <router-view :key="$route.fullPath" />
</template>

<script>
import 'bootstrap/dist/css/bootstrap.css';
import 'bootstrap/dist/js/bootstrap';
import NarBar from './components/NavBar.vue';

export default {
  name: "App",
  components: {
    NarBar
  }
}

</script>

<style></style>
