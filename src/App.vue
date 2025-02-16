<template>
  <div>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App',
};
</script>

<style>
/* Dodaj style globalne tutaj */
#app, body, #app > div, #app > div > div {
  height: 100vh;
  width: 100vw;
}
body {
  transition: background-color 0.5s, color 0.5s;
}
body.dark {
  background-color: #1a202c;
  color: #a0aec0;
}
</style>
