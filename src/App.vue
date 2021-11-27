<template>
  <Main />
</template>

<script>
import Main from './pages/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style lang="scss">
  @font-face {
    font-family: OpenSans;
    src: url(./assets/fonts/OpenSans.ttf);
    font-weight: 400;
  }
body {
  padding: 0;
  margin: 0;
  font-family: OpenSans;
}
* {
  box-sizing: border-box;
}

</style>
