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

<style lang="scss">
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

html {
  font-size: 62.5%;

  @media(max-width: 1260px) {
    font-size: 60%;
  }

  @media(max-width: 1100px) {
    font-size: 58%;
  }

   @media(max-width: 950px) {
    font-size: 56%;
  }

  @media(max-width: 700px) {
    font-size: 55%;
  }

  @media(max-width: 500px) {
    font-size: 54%;
  }
}

body {
  line-height: 1.7;
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  color: #222;
  font-size: 1.6rem;
}


</style>
