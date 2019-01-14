<template>
  <div id="app" class="container">
    <Calculator api="apilink"/>
  </div>
</template>

<script>
import Calculator from './components/Calculator.vue';
import ('./assets/theme-1.css');
export default {
  name: 'app',
  components: {
    Calculator
  }
}
</script>

<style>
</style>
