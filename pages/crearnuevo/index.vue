<template>
    <v-container>
        <v-container>
            <h2>Crear nuevo registro</h2>
            <p>Este modulo no esta en funcion por ahora:</p>
        </v-container>
        <v-container>
            <v-form @submit.prevent="crear">
                <v-row>
                    <v-col>
                        <v-text-field
                            v-model="valorNuevo"
                            label="Valor nuevo"
                            hint="Valor nuevo"
                            outlined
                        >
                        </v-text-field>
                    </v-col>
                    <v-col>
                        <v-select
                            v-model="select"
                            :hint="`${select.nombre}`"
                            :items="items"
                            item-text="nombre"
                            item-value="nombre"
                            label="Crear en..."
                            persistent-hint
                            return-object
                            single-line
                        ></v-select>
                    </v-col>
                    <v-col v-if="select.posicion===0">
                        <v-select
                            value="idestado"
                            :items="idsestados"
                            label="IDs estados"
                            outlined
                        ></v-select>
                    </v-col>
                    <v-col v-if="select.posicion===1">
                        <v-select
                            value="idmunicipio"
                            :items="idsmunicipio"
                            label="IDs municipios"
                            outlined
                        ></v-select>
                    </v-col>
                    <v-col v-if="select.posicion===1">
                        <v-select
                            value="idcp"
                            :items="idscodigospostales"
                            label="IDs CPs"
                            outlined
                        ></v-select>
                    </v-col>
                    <v-col>
                        <v-btn
                            elevation="2"
                            color="dark"
                            type="submit"
                        >Crear</v-btn>
                    </v-col>
                </v-row>
            </v-form>
        </v-container><!--Form-->
        <v-container>
            <h3>Resultado:</h3>
            <p>{{resultado}}</p>
        </v-container>
    </v-container>
</template>

<script>
export default {
    async mounted(){
        this.idsestados = await this.getIDs('estados');
        this.idsmunicipio = await this.getIDs('municipios');
        this.idscodigospostales = await this.getIDs('codigospostales');
        this.idscolonias = await this.getIDs('colonias');
    },
    data(){
        return{
            valorNuevo : '',
            select: {nombre : '', valor : '', posicion : -1},
            items : [
                {nombre : 'Municipio', valor : 'municipio', posicion : 0},
                {nombre : 'Colonia', valor : 'colonia', posicion : 1},
                {nombre : 'Codigos postales', valor : 'codigopostal', posicion : 2},
            ],
            idsestados : [],
            idsmunicipio : [],
            idscodigospostales : [],
            idscolonias : [],
            
            idestado : '',
            idmunicipio : '',
            idcp : '',
            idcolonia : '',
            
            resultado : '',
        }
    },
    methods : {
        async crear(){
            const url = `http://127.0.0.1:5000/${this.select.valor}/`;
            const sendData = new FormData();
            sendData.append('id', this.valorNuevo);
            if(this.select.posicion!==2 && this.select.posicion!==-1){
                sendData.append('nombre', this.valorNuevo);
            }
            if(this.select.posicion===1){
                sendData.append('idmunicipio', this.idmunicipio);
                sendData.append('idcp', this.idcp);
            }else if(this.select.posicion===0){
                sendData.append('idestado', this.idestado);
            }
            
            const peticion = await fetch(url,{
                method : 'POST',
                body : sendData,
                'mode': 'no-cors',
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                },
            });

            if(peticion.status===404){
                this.resultado = 'Vaya, algo salio mal, intente de nuevo';
            }else if(peticion.status===500){
                this.resultado = 'Vaya, hubo un error con el servidor';
            }else if(peticion.status===200){
                data = await peticion.json();
                this.resultado = `Listo, se ha creado registro para ${this.select.nombre}`;
            }            

        },
        async getIDs(tabla){
            const url = `http://127.0.0.1:5000/${tabla}/`;
            const peticion = await fetch(url);
            const data = await peticion.json();
            const resultadoIDS =[];
            if(data){
                for (const key in data) resultadoIDS.push(data[key]['id']);
            }

            return resultadoIDS;

        }
    }
}
</script>

<style>

</style>