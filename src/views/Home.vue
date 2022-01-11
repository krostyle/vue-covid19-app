<template>
  <main v-if="!loading">    
    Show Data
  </main>
  <main v-else class="flex flex-col align-center justify-center text-center">
    <button type="button" class="bg-indigo-500 inline-flex items-center px-4 py-2 font-semibold leading-6 text-sm
    shadow rounded-md text-white hover:bg-indigo-400 transition ease-in-out duration-150 cursor-not-allowed" disabled>
      <svg class="animate-spin h-5 w-5 mr-3" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" fill="none">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>      
      </svg>
      Processing...
    </button>
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

</style>
