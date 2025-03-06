<template>
  <div class="app-container">
    <main class="w-full">
      <router-view></router-view>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App'
};
</script>

<style>
.app-container {
  min-height: 100vh;
  width: 100%;
  max-width: 100vw;
  overflow-x: hidden;
}

body,
html {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
  width: 100%;
}
</style>
