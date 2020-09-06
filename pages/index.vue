<template>
  <v-app>
    <CardWeather @save="getDataFromLocalStorage"/>
    <v-divider v-if="dataFromLocalStorage.length"></v-divider>
    <TableDataSaveWeather :data="dataFromLocalStorage" @remove-item="deleteItem"/>
  </v-app>
</template>

<script>
import CardWeather from '~/components/CardWeather.vue'
import TableDataSaveWeather from '~/components/TableDataSaveWeather.vue'

export default {
  data () {
    return {
      dataFromLocalStorage: []
    }
  },
  methods: {
    getDataFromLocalStorage() { 
      this.dataFromLocalStorage = []
      let keys = Object.keys(localStorage)            
      for (let i = 0; i < keys.length; i++) {
        let item = JSON.parse(keys[i])        
        this.dataFromLocalStorage.push(item)
      }      
    },  

    deleteItem(index) {
      this.dataFromLocalStorage = this.dataFromLocalStorage.filter(
        (item, idx) => idx !== index
      )
    }
  },
  mounted() {     
    this.getDataFromLocalStorage()
  },
  
  components: {
    CardWeather,
    TableDataSaveWeather
  }
}
</script>
