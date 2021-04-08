<template>
    <v-container>
        <v-container>
            <h2>Buscar colonia por Codigo Postal</h2>
        </v-container>
        <v-container>
            <v-form @submit.prevent="buscar">
                <v-row>
                    <v-col>
                        <v-text-field
                            v-model="codigopostal"
                            label="Codigo postal"
                            hint="Codigo postal"
                            outlined
                        >
                        </v-text-field>
                    </v-col>
                    <v-col>
                        <v-btn
                            elevation="2"
                            color="dark"
                            @click="buscar()"
                        >Buscar</v-btn>
                    </v-col>
                </v-row>
            </v-form>
        </v-container>
        <v-container>
            <h3>Resultados</h3>
        </v-container>
        <v-container>
            <v-list>
                <v-list-item
                    v-for="(resultado, index) in resultado"
                    :key="index"
                >
                    <v-list-item-content>
                        <v-list-item-title v-text="resultado.valor"></v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
            </v-list>
        </v-container>
    </v-container>
</template>

<script>
export default {
    data(){
        return{
            codigopostal : '',
            resultado : []
        };
    },
    methods : {
        async buscar(){
            /* ALTER TABLE `colonia` CHANGE `id` `id` INT(10) NOT NULL AUTO_INCREMENT; */
            const peticion = await fetch(`http://127.0.0.1:5000/coloniacp/${this.codigopostal}`);
            let data = null;
            if(peticion.status===404){
                this.resultado.push({
                    valor : 'No se encontraron colonias'
                });
            }else if(peticion.status===500){
                this.resultado.push({
                    valor : 'Vaya, hubo un error con el servidor'
                });
            }else if(peticion.status===200){
                data = await peticion.json();
                this.resultado = []
                for (const key in data) this.resultado.push({
                    valor : data[key]['nombre']
                });
            }
        },
    }
}
</script>

<style>

</style>