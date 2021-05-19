<template>
  <div>
    <h1>Hotels</h1>
    <ul>
      <li v-for="(item, index) in hotels" :key="index">{{ item.name }}  {{ item.destinationId }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Hotels',
  props: {
    hotels: {
      type: Array,
      default: () => []
    }
  }
}
</script>

<style>

</style>