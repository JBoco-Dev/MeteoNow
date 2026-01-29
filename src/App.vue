<template>
  <div id="app">
   <meteo></meteo>
  </div>
</template>

<script>
import Meteo from './components/Meteo'

export default {
  name: 'App',
  components: {
    'meteo': Meteo
  }

}
</script>

<style></style>
