<template>
  <div id="app">
    <Header/>
    <Form/>
    <AppFooter/>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Form from './components/Form.vue'
import AppFooter from './components/AppFooter.vue'

export default {
  name: 'App',
  components: {
    Header,
    Form,
    AppFooter
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
