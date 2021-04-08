<template>
    <v-container>
        <v-container>
            <h2>Buscar estado, municipio y colonia por nombre</h2>
        </v-container>
        <v-container>
            <v-form @submit.prevent="buscar">
                <v-row>
                    <v-col>
                        <v-select
                            v-model="select"
                            :hint="`${select.nombre}`"
                            :items="items"
                            item-text="nombre"
                            item-value="valor"
                            label="Buscar por..."
                            persistent-hint
                            return-object
                            single-line
                        ></v-select>
                    </v-col>
                    <v-col>
                        <v-text-field
                            v-model="nombre"
                            :label="`Nombre de ${select.nombre}`"
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
            nombre : '',
            select: {nombre : '', valor : ''},
            items : [
                {nombre : 'Estado', valor : 'estado'},
                {nombre : 'Municipio', valor : 'municipio'},
                {nombre : 'Colonia', valor : 'colonia'},
            ],
            resultado : [],

        };
    },
    methods : {
        async buscar(){
            const url = `http://127.0.0.1:5000/${this.select.valor}nombre/${this.nombre}`;
            const peticion = await fetch(url);
            let data = null;
            if(peticion.status===404){
                this.resultado.push({
                    valor : `No se encontraron resultados para ${this.select.nombre}`
                });
            }else if(peticion.status===500){
                this.resultado.push({
                    valor : 'Vaya, hubo un error con el servidor, intente mas tarde'
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