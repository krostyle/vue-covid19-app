<template>
  <div class="home">    
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import csv from 'csvtojson'

export default {
  name: 'Home',
  components: {    
  },
  methods:{
    async fetchCovidData(){
      const {data} = await axios.get('https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto1/Covid-19.csv')      
      const json = await csv({
        noheader:false,
        output: "json",        
      }).fromString(data)                        
      return data
    }
  },
  async created(){
    const data =await this.fetchCovidData()    
  }
}
</script>
