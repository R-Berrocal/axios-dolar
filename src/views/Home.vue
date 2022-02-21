<template>
  <div>
  <v-layout :wrap="true">
    <v-flex xs12>
      <v-card>
        <v-date-picker 
        v-model="fecha"
        full-width
        locale="es-cl"
        :min="minimo"
        :max="maximo"
        @change="getDolar(fecha)"
        ></v-date-picker>
      </v-card>
      <v-card color="error" dark> 
        <v-card-text class="display-1 text-center">
          {{dolar}}
        </v-card-text>
      </v-card>
    </v-flex>
    
  </v-layout>
  </div>
</template>

<script>
  import axios from "axios";
  import {mapMutations} from "vuex"
  export default {
    data(){
      return{
        fecha:new Date().toISOString().substring(0,10),
        minimo:"1984",
        maximo: new Date().toISOString().substring(0,10),
        dolar:null
      }
    },
    methods:{
      ...mapMutations(["mostrarLoading","ocultarLoading"]),
      async getDolar(dia){
       
        let arrayFecha= dia.split("-").reverse().join("-");
        console.log(arrayFecha)

        try {
          this.mostrarLoading({titulo:"accediendo a informacion",color:"secondary"})
          const datos= await axios.get(`https://mindicador.cl/api/dolar/${arrayFecha}`);

          if(datos.data.serie.length > 0){
            this.dolar=await datos.data.serie[0].valor;
          }else{
            this.dolar="sin resultados"
          }
           
        } catch (error) {
          console.log(error)
        }
        finally{
          this.ocultarLoading()
        }
      }
    },
    created(){
      this.getDolar(this.fecha);
    }
  }
</script>
