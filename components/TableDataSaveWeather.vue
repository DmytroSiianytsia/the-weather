<template>
  <v-simple-table fixed-header height="300px" v-if="data.length">
    <template v-slot:default>
      <thead>
        <tr>
          <th class="text-left">Date</th>
          <th class="text-left">City</th>
          <th class="text-left">Temp</th>
          <th class="text-left">Wind</th>
          <th class="text-left">Humidity</th>
          <th class="text-left"></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in data" :key="index">
          <td>{{ item.date }}</td>
          <td>{{ item.city }}</td>
          <td>{{ item.temp }}&deg;C</td>
          <td>{{ item.windSpeed }} m/s</td>
          <td>{{ item.humidity }} %</td>
          <td class="close" @click="removeItemFromLocalStorage(item, index)">&times;</td>
        </tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
  export default {
    props: ['data'],
    methods: {
      removeItemFromLocalStorage(item, index) {
        let key = JSON.stringify(item)        
        localStorage.removeItem(key)
        this.$emit('remove-item', index)             
      },
    },
  }
</script>

<style>
  .close {
    cursor: pointer;
  }
</style>
