<template>
  <div id="app">
    <UserList />
  </div>
</template>

<script>
  import UserList from './components/UserList.vue';
  export default {
    name: 'app',
    components: {
      UserList
    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Roboto:400,500,700');
  #app {
    margin: 0;
    padding: 0;
    background: #f6f6f6;
    font-family: 'Roboto', sans-serif;
  }
</style>
