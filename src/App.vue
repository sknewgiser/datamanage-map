<template>
  <div id="app">
    <app-pane></app-pane>
    <app-map></app-map>
  </div>
</template>

<script>
import Map from './views/Map'
import pane from './components/pane'
export default {
  name: 'App',
  components: {
    'app-map': Map,
    'app-pane': pane
  },
  data () {
    return {
    }
  }
}
</script>

<style scoped>
/* #app{
  background-color: rebeccapurple;
} */
</style>
