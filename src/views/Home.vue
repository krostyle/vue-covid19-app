<template>
  <main v-if="!loading">    
    <Title :title="title"/>
    <div class="max-w-2xl mx-auto">
      <div class="my-5 mx-auto p-4 max-w-full bg-white rounded-lg border shadow-md sm:p-8 dark:bg-gray-800 dark:border-gray-700">
        <div class="flex justify-between items-center mb-4">
          <h1 class="text-xl mx-auto font-bold leading-none text-gray-900 dark:text-white">Categorias</h1>                    
        </div>
        <div class="flow-root">
          <ul role="list" class="divide-y divide-gray-200 dark:divide-gray-700">
            <li v-for="(data, index) in lastDataCovid" :key="index" class="py-3 sm:py-4">
                <div class="flex items-center space-x-4">                                      
                    <div class="flex-1 min-w-0">
                        <p class="text-sm font-medium text-gray-900 truncate dark:text-white">
                            {{data.nombre}}
                        </p>
                        <p class="text-sm text-gray-500 truncate dark:text-gray-400">
                            {{data.fecha}}
                        </p>
                    </div>
                    <div class="inline-flex items-center text-base font-semibold text-gray-900 dark:text-white">
                        {{data.casos}}
                    </div>
                </div>
            </li>
          </ul>  
        </div>
      </div>
    </div>
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
import Title from '@/components/Title'
import moment from 'moment'

export default {
  name: 'Home',
  components: {    
    Title
  },
  data(){
    return {
      loading:true,
      title:'Información de Covid Chile',
      data:[],    
      lastDataCovid:[]  
    }
  },
  methods:{
    async fetchCovidData(){
      const {data} = await axios.get('https://raw.githubusercontent.com/MinCiencia/Datos-COVID19/master/output/producto5/TotalesNacionales.csv')             
      const json = await csv().fromString(data);
      const data_json=json.map(d=>{
        const  {
          ['Fecha']:nombre,
          ...fechaCasos
        } = d
        const fechas=Object.keys(fechaCasos)
        const casos=Object.values(fechaCasos)
        return {
          nombre,
          fechas,
          casos
        }
      })
      return data_json
    },
    lastData(data_json){      
      const lastData=data_json.map(data=>{
        const lastDataNumber=(data.casos.length-1);
        const fecha = moment(new Date(data.fechas[lastDataNumber])).format('DD/MM/YYYY')
        const casos = data.casos[lastDataNumber] ? parseFloat(data.casos[lastDataNumber]).toLocaleString('es-CL') : 0
        return {
          nombre:data.nombre,
          fecha,
          casos
        }
      })
      console.log(lastData);
      return lastData
    }
  },
  async created(){
    const data =await this.fetchCovidData()    
    this.data=data
    this.lastDataCovid=this.lastData(data)
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
