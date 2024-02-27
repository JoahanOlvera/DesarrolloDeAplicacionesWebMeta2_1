<template>
    <div class="mt-4 align-center text-subtitle-2"> </div>
    <v-card class="mx-auto" title="Imagen sacada de Picsum" max-width="300">
        <v-img :src="urlImagen" height="400px" cover></v-img>
        <v-card-actions>
            <v-btn color="red" @click="cambiaImagen">Nueva Imagen y Chiste</v-btn>
        </v-card-actions>
    </v-card>
    <div class="d-flex align-center flex-column">
        <div class="mt-4 text-subtitle-2"> </div>
        <v-card color="red" width="400" title="Bromas de Chuck Norris" :text="joke"></v-card>
    </div>
</template>

<script setup>
import { ref } from 'vue'

const urlImagen = ref("https://picsum.photos/500");

async function cambiaImagen() {
    let respuesta = await fetch("https://picsum.photos/500/250");
    console.log(respuesta.url)
    urlImagen.value = respuesta.url;
    joke.value = await fetchChuckNorrisJoke();
}

const joke = ref("");

async function fetchChuckNorrisJoke() {
    const response = await fetch('https://api.chucknorris.io/jokes/random');
    const data = await response.json();
    return data.value;
}

fetchChuckNorrisJoke().then(fetchedJoke => {
    joke.value = fetchedJoke;
});

</script>