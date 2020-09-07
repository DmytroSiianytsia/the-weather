<template>
<v-row>
  <v-card
    class="mx-auto mt-10 mb-10"
    width="360"
    min-width="320"
    height="500"       
    v-if="dataWeather"    
  >
  
    <v-list-item two-line>
      <v-list-item-content>
        <v-list-item-title class="headline">
          {{ notFound ? `city not found` : dataWeather.name }}
        </v-list-item-title>
        <v-list-item-subtitle>{{today.day}}</v-list-item-subtitle>
      </v-list-item-content>
    </v-list-item>

    <v-card-text>
      <v-row align="center">
        <v-col class="display-2" cols="6">
          {{ notFound ? 0 : temp }}&deg;C
        </v-col>
        <v-col cols="6">
          <v-img
            :src="icon"
            alt="Sunny image"
            width="92"
          ></v-img>
        </v-col>
      </v-row>
    </v-card-text>

    <v-list-item>
      <v-list-item-icon>
        <v-icon>mdi-send</v-icon>
      </v-list-item-icon>
      <v-list-item-subtitle>{{notFound ? 0 : dataWeather.wind.speed}} m/s</v-list-item-subtitle>
    </v-list-item>

    <v-list-item>
      <v-list-item-icon>
        <v-icon>mdi-cloud-download</v-icon>
      </v-list-item-icon>
      <v-list-item-subtitle>{{notFound ? 0 : dataWeather.main.humidity}}%</v-list-item-subtitle>
    </v-list-item>       

    <v-divider></v-divider>

    <v-card-actions>
      <v-container>
        <v-row justify="space-between">
          <v-col cols="12">

            <v-form ref="form" @submit.prevent>
              <v-text-field
                v-model="city"                
                label="City"
                @keydown.enter="fetchApiWeather"
              ></v-text-field>

              <v-row>
                <v-col cols="6 text-center">
                  <v-btn class="info" @click="fetchApiWeather">
                    Load
                  </v-btn>
                </v-col>

                <v-col cols="6 text-center">
                  <v-btn class="info" @click="addSelectedCity">
                    Add
                  </v-btn>
                </v-col>                              
              </v-row>              
            </v-form>

          </v-col>      
        </v-row>
      </v-container>
    </v-card-actions>
  </v-card>

  <v-card
    class="mx-auto mt-10 mb-10"    
    min-width="400"
    max-height="500"
    v-if="selectedCities.length"
  >
    <v-simple-table>
      
        <thead>
          <tr>
            <th class="text-left">City</th>
            <th class="text-left">Temperature</th>            
            <th class="text-left"></th>            
            <th class="text-left"></th>            
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in selectedCities" :key="index">
            <td>{{ item.city }}</td>
            <td>{{ item.temp }}</td>
            <td>
              <v-col cols="6 text-center">
                <v-btn class="info" @click="emitData(index)" x-small>
                  Save
                </v-btn>
              </v-col>
            </td>
            <td>
              <v-col cols="6 text-center">
                <v-btn class="info" @click="deleteSelectedCity(index)" x-small>
                  Del
                </v-btn>
              </v-col>
            </td>
          </tr>
        </tbody>      
    </v-simple-table>
  </v-card>
</v-row>  
</template>

<script>
  export default {
    data () {
      return {
        dataWeather: '',
        city: 'kiev',
        notFound: false,
        dataForLocalStorage: {},
        keyForLocalStorage: 0,
        selectedCities: []
      }
    },
    computed: {
      temp: function() {
        if (this.dataWeather) {
          return Math.round(this.dataWeather.main.temp)
        }
      },
      icon: function() {
        if (this.dataWeather) {          
          return `http://openweathermap.org/img/wn/${this.dataWeather.weather[0].icon}@2x.png`
        }
      },
      today: function() {
        const newDate = new Date()
        return { day: newDate.toLocaleString(
          'en-GB',
          {
            weekday: 'long',
            year: 'numeric',
            month: 'long', 
            day: 'numeric', 
            hour: '2-digit',
            minute: '2-digit'           
          }
        )} 
      }
    },    
    methods: {
      async fetchApiWeather() {        
        try {
          const apiWeather = await fetch(
            `http://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=81d69a4ca07e95f1014217cfe92ea160&units=metric`
          )
          
          if (apiWeather.status === 200) {
            this.dataWeather = await apiWeather.json()
            this.notFound = false
            this.formDataForLocalStorage()                                
          } else {
            this.notFound = true
          } 
          
          this.clearCity()
        } catch(e) {
          throw e
        }                     
      },
      formDataForLocalStorage() {
        this.dataForLocalStorage = {
          city: this.dataWeather.name,
          date: this.today.day,
          temp: this.temp,
          windSpeed: this.dataWeather.wind.speed,
          humidity: this.dataWeather.main.humidity,                     
        }   
      },
      addSelectedCity() {
        let set = new Set(this.selectedCities)
        set.add(this.dataForLocalStorage)
        this.selectedCities = [...set]        
      },
      deleteSelectedCity(index) {        
        this.selectedCities = this.selectedCities.filter((item, idx) => idx !== index)
      },
      clearCity() {
        this.city = ''
      },
      saveDataWeather(index) {        
        const value = JSON.stringify(this.selectedCities[index])        
        localStorage.setItem(value, value)        
      },
      emitData(index) {
        this.saveDataWeather(index)
        this.$emit('save', this.selectedCities[index])        
      } 
    },
    mounted() {      
      this.fetchApiWeather()      
    },
  }
</script>
