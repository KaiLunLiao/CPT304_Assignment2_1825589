<template>
  <div>
    <h1>Weather</h1>
    <p v-if="weather">
      <img :src="weather.icon" alt="">
      <strong>{{ weather.text }}</strong>
    </p>
    <p v-else>No Result</p>
  </div>
</template>

<script>
export default {
  name: 'Weather',
  props: {
    weather: {
      type: Object,
      default: () => null
    }
  },
  data() {
    return {
      
    }
  }
}
</script>

<style>

</style>