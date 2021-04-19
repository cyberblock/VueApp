<template>
<div>
    <v-layout :wrap="true">
        <v-flex xs12>
            <v-date-picker
                v-model="date"
                full-width
                locale="es-cl"
                :min="minimo"
                :max="maximo"
                class="mt-4"
                @change="getDolar(date)"
                ></v-date-picker>
            <v-card color="error" dark>
               <v-card-text class="display-1 text-center">
                   {{valor}}$ 
                </v-card-text>
            </v-card>
        </v-flex>
    </v-layout>
    
</div>
</template>

<script>
import axios from 'axios'
import { mapMutations } from "vuex";
export default {
    data(){
        return{
            date: new Date().toISOString().substr(0,10),
            minimo: '1984',
            maximo: new Date().toISOString().substr(0,10),
            valor: null
        }
    },
    methods:{
        ...mapMutations(['mostrarLoading','ocultarLoading']),
        async getDolar(dia){
            let arrayFecha = dia.split(['-'])
            let ddmmyy = arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0];

            try {
                this.mostrarLoading({titulo:'Accediendo a informacion', color:'secondary'})
            let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`)

            if (datos.data.serie.length > 0) {
                this.valor = await datos.data.serie[0].valor
            } else {
                this.valor ='Sin resultado'
            }

        
            } catch (error) {
                //console.log(error);
            }finally{
                this.ocultarLoading()
            }
           
            
        }
    },
    created(){
        this.getDolar(this.date)
    }
}
</script>