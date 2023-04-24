<template>
  <MainPage />
</template>

<script>
import MainPage from './components/MainPage.vue';

export default {
  name: 'App',
  components: {
    MainPage,
  },
};
</script>

<style>
/* CSS RESET */
html,
body {
  height: 100%;
  margin: 0;
  font-family: Pretendard, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
p {
  margin: 0;
}
* {
  font-family: inherit;
}

/* WEB FONT */
@import url('https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.6/dist/web/static/pretendard-dynamic-subset.css');

#app {
  height: 100%;
}
</style>
