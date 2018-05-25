<template>
  <div id="app">
    <login-comp></login-comp>
  </div>
</template>

<script>

import Login from './components/login/Login.vue';

export default {
  components:{
    "login-comp": Login
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<style lang="scss">

</style>
