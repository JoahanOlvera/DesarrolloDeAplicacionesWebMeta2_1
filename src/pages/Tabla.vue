<template>
    <v-card
      flat
      title="Datos en latín"
    >
      <template v-slot:text>
        <v-text-field
          v-model="search"
          label="Buscar"
          prepend-inner-icon="mdi-magnify"
          single-line
          variant="outlined"
          hide-details
        ></v-text-field>
      </template>
  
      <v-data-table
        :headers="headers"
        :items="frases"
        :search="search"
      ></v-data-table>
    </v-card>
  </template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      search: '',
      frases: [],
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Título', value: 'title' },
        { text: 'Completado', value: 'completed' }
      ]
    };
  },
  mounted() {
    axios.get('https://jsonplaceholder.typicode.com/todos')
      .then(response => {
        this.frases = response.data;
      })
      .catch(error => {
        console.error('Error al obtener datos:', error);
      });
  }
};
</script>