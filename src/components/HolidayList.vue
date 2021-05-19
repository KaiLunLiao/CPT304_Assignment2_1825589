<template>
  <div  v-if="holidays && holidays.length > 0">
    <h2>{{ country }} holidays</h2>
    <ul class="holidays">
      <li @click="handleClickItem(item)" v-for="item in holidays" :key="item.name" :style="{background: item.name === choosed ? 'blue' : ''}">
        {{ item.name }} {{ item.date }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "HolidayList",
  props: {
    country: {
      type: String,
      default: () => ''
    },
    holidays: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      choosed: null
    }
  },
  methods: {
    handleClickItem(item) {
      this.choosed = item.name;
      this.$emit('chooseHoliday', item);
    }
  }
};
</script>

<style scoped>
.holidays {
  height: 300px;
  overflow: auto;
}
</style>
