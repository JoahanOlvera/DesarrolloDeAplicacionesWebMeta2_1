<template>
      <v-layout>
        <v-app-bar title="Barra de tareas">
      <template v-slot:prepend>
         <v-app-bar-nav-icon @click.stop="barOpen = !barOpen" ></v-app-bar-nav-icon>
      </template>
      <v-spacer></v-spacer>
    </v-app-bar>  
        <v-navigation-drawer v-model="barOpen">
              <v-list>
                  <v-list-item prepend-icon="mdi-vuetify" title="Meta 2.1" subtitle="Usando vuetify"></v-list-item>
              </v-list>
              <v-divider></v-divider>
              <v-list density="compact" nav>
                <v-list-item prepend-icon="mdi-home" link to="/" title="Inicio" value="Inicio"></v-list-item>
                <v-list-item prepend-icon="mdi-image-multiple" link to="/ImagenesYChistes" title="Imagenes y Chistes" value="myfiles"></v-list-item>
                <v-list-item prepend-icon="mdi-text-box-outline" link to="/Tabla" title="Tabla de datos" value="myfles"></v-list-item>
                <v-list-item prepend-icon="mdi-text-box-outline" link to="/TablaCRUD" title="Tabla de datos CRUD" value="myfiles"Z></v-list-item>
              </v-list>
          </v-navigation-drawer>

          <v-main style="height: 1000px">
              <RouterView />
          </v-main>
      </v-layout>
</template>

<script setup>
  import { ref } from 'vue';
  const barOpen = ref(false);
</script>
