<template>
  <div>
    <h1 class="base" @click="visible1 = true">base</h1>
    <picker class="base-picker" v-model="visible1" :data-items="baseItems" @confirm="confirm" @change="onBaseValuesChange"></picker>
  </div>
</template>

<script>

export default {
  data() {
    return {
      visible1: false,
      baseItems: [{
        values: ['2000', '2001', '2002', '2003', '2004', '2005', '2006', '2007']
      }, {
        values: ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12']
      }]
    }
  },
  mounted: function() {
  },
  methods: {
    onBaseValuesChange (year, month) {
      console.log(year, month);
    },
    confirm (year, month) {
      console.log(year, month);
    }
  }
}
</script>

<style scoped>

</style>
