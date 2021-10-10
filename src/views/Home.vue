<template>
  <div class="home">
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

<style>
.home{
  width: 70%;
  margin: auto
}
</style>
