<template lang="pug">
  #app
    comment-app-container
</template>

<script>
import CommentAppContainer from './components/CommentAppContainer.vue'
import './styles/global.scss'

export default {
  name: 'App',
  components: {
     CommentAppContainer
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
