<template>
<v-container>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-select
          v-model="select"
          :hint="`${select.nombre}`"
          :items="items"
          item-text="nombre"
          item-value="valor"
          label="Select"
          persistent-hint
          return-object
          single-line
      ></v-select>
      <v-btn
        elevation="2"
        color="dark"
        @click="mostrar()"
      >Mostrar</v-btn>
    </v-col>
  </v-row>
  <v-container>
    <h3>{{select.nombre}}</h3>
  </v-container>
  <v-row>
    <v-col>
      <v-list>
        <v-list-item
          v-for="(entidad, index) in dataPeticion"
          :key="index"
        >
          <v-list-item-content>
            <v-list-item-title v-text="entidad.valor"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-col>
  </v-row>
</v-container>
</template>

<script>

export default {
  components: {
    
  },
  data(){
    return{
      select: {nombre : 'Estados', valor : 'estados'},
      items : [
        {nombre : 'Estados', valor : 'estados'},
        {nombre : 'Municipios', valor : 'municipios'},
        {nombre : 'Colonias', valor : 'colonias'},
        {nombre : 'Codigos postales', valor : 'codigospostales'},
      ],
      dataPeticion : []
    };
  },
  methods : {
    mostrar(){
      console.log('Mostrar');
      console.log(this.select);
      this.peticionRegistrosCompletos(this.select.valor);

    },
    peticionRegistrosCompletos : async function(ruta){
      
      const peticion = await fetch(
        `http://127.0.0.1:5000/${ruta}/`);
      
      // Data de flask
      const data = await peticion.json();
      
      if(data){
        this.dataPeticion = []
        console.log(data);
        const llave = ruta==='codigospostales'?'id':'nombre';
        for (const key in data) this.dataPeticion.push({
          valor : data[key][llave]
        });
      }
      console.log(this.dataPeticion);
    },
  }
}
</script>
