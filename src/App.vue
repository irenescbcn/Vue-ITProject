<template>
  <div id="app">
    <Rover></Rover>
  </div>
</template>


<script>
// @ is an alias to /src
import Rover from '@/components/Rover.vue'

export default {
  name: 'Home',
  components: {
    Rover
  }
}
</script>

<style lang="scss">
#app {
  width: 70%;
  margin: auto;
  text-align: center;
}


</style>
