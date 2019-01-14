<template>
  <div class="calculator">
    <div class="calculator__display">0</div>
    <CalculatorKeys />
  </div>
</template>

<script>
import CalculatorKeys from './CalculatorKeys.vue';

export default {
  name: 'Calculator',
  components: {
    CalculatorKeys
  },
  data () {
    return {
      info: null
    }
  }
}
</script>

<style>
</style>
