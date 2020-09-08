<template>
  <div class="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: "app"
};
</script>

<style>
@import "https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css";

* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

html {
  font-size: 62.5%;
}

body {
  line-height: 1.7;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  color: #222;
  font-size: 1.6rem;
  font-weight: normal;
}

.app {
}
</style>
