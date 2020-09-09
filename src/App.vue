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
  overflow: hidden;
}

</style>
