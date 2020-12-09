<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: "App",
};
</script>

<style>
#app {
  background-image: url("/static/bg.png");
  background-size: cover;
  background-repeat: no-repeat;
  height: 100vh;
}

a:hover {
  text-decoration: none;
}
</style>
