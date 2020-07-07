<template>
 <div>
   <v-container fluid>
    <v-row>
      <v-col class="d-flex" cols="12" sm="12">
        <v-select
          v-model="select"
          :items="country"
          outlined
          label="Selecione un pais"
        ></v-select>
      </v-col>
    </v-row>
   </v-container>
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
      select: '',
      text: '',
      country:[
        {value: 'RD', text: "Republica Dominicana"},
        {value: 'CH', text: "Chile"},
        {value: 'MX', text: "Mexico"},
        {value: 'AR', text: "Argentina"},
      ]

    }
  },
  methods: {
    ...mapMutations(['mostrarLoading', 'ocultarLoading']),

   async getDolar(dia){

     let arrayFecha = dia.split(['-']);
     let ddmmyy = dia.split('-').reverse().join('-');
     let city 
     switch (this.select) {
         case 'RD':
           city = 0.073
           break;
         case 'CH':
           city = 1
           break;
         case 'MX':
           city = 0.028
           break;
         case 'AR':
           city = 0.089
           break;
     }
     try {
       
      this.mostrarLoading({titulo:'Accediendo a la información', color:'secondary'})

      let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)
      if(datos.data.serie.length > 0){
        this.valor = await datos.data.serie[0].valor * city
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
