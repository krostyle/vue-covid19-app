<template>
  <main v-if="!loading">    
    Show Data
  </main>
  <main v-else class="flex justify-center items-center">
    <div class="loader animate-spin ease-linear rounded-full border-8 border-t-8 border-gray-300 h-64 w-64">      
    </div>      
  </main>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import csv from 'csvtojson'

export default {
  name: 'Home',
  components: {    
  },
  data(){
    return {
      loading:true,
      title:'Totales Chile',
      data:[],      
    }
  },
  methods:{
    async fetchCovidData(){
      const {data} = await axios.get('https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto1/Covid-19.csv')            
      const json = await csv({
        noheader:false,
        output: "json",        
      }).fromString(data)                        
      return json
    }
  },
  async created(){
    const data =await this.fetchCovidData()    
    this.data=data.map(d=>{      
      const {
        ['Codigo comuna']:codigo_comuna,
        ['Codigo region']:codigo_region,
        ['Comuna']:comuna,
        ['Poblacion']:poblacion,
        ['Region']:region,
        ['Tasa']:tasa,
        ...fechas}=d
      return {
        codigo_comuna,
        codigo_region,
        comuna,
        poblacion,
        region,
        tasa,
        fechas
      }      
    })    
    this.loading=false
  }
  
}
</script>

<style scoped>
.loader {
  border-top-color: rgb(99 102 241);
  -webkit-animation: spinner 1.5s linear infinite;
  animation: spinner 1.5s linear infinite;
}

@-webkit-keyframes spinner {
  0% { -webkit-transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); }
}

@keyframes spinner {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
