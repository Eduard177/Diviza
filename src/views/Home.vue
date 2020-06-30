<template>
 <div>
 <v-layout :wrap="true">
   <v-flex xs12>
     <v-card>
       <v-date-picker 
       color="blue"
       v-model="fecha"
       full-width
       locale="es-mx"
       :min="minimo"
       :max="maximo"
       @change="getDolar(fecha)"
       ></v-date-picker>
     </v-card>
     <v-card color="error" dark>
       <v-card-text class="display-1 text-center">
         {{valor}} 
       </v-card-text>
     </v-card>
   </v-flex>
 </v-layout>
 </div>
</template>

<script>

import axios from 'axios';
import {mapMutations} from "vuex";

export default {
  name:'Home',
  data() {
    return {
      fecha: new Date().toISOString().substr(0,10),
      minimo: '1984',
      maximo: new Date().toISOString().substr(0,10),
      valor: null,
    }
  },
  methods: {
    ...mapMutations(['mostrarLoading', 'ocultarLoading']),

   async getDolar(dia){

     let arrayFecha = dia.split(['-']);
     let ddmmyy = dia.split('-').reverse().join('-');
     try {

      this.mostrarLoading({titulo:'Accediendo a la información', color:'secondary'})

      let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)
      if(datos.data.serie.length > 0){
        this.valor = await datos.data.serie[0].valor * 0.07162
      }else{
        this.valor = 'Sin resultados'
      }
     } catch (error) {
       console.log(error);
     }
     finally{
        this.ocultarLoading()
     }
      
    }
  },
  created() {
    this.getDolar(this.fecha)
  },
}
</script>
