<template>
  <v-card flat title="Datos en latín">
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

    <v-data-table :headers="headers" :items="frases" :search="search"></v-data-table>
  </v-card>

  <v-container class="d-flex align-center justify-center">
    <v-btn @click="abrirDialogoAgregarItem">Añadir item</v-btn>
    <v-btn @click="abrirDialogoActualizarItem">Cambiar item</v-btn>
    <v-btn @click="abrirDialogoBorrarItem">Borrar item</v-btn>
  </v-container>

  <!-- Diálogo para agregar un nuevo item -->
  <v-dialog v-model="dialogAgregarItem" max-width="500">
    <v-card>
      <v-card-title>Añadir Nuevo Item</v-card-title>
      <v-card-text>
        <v-text-field v-model="nuevoItem.title" label="Título"></v-text-field>
        <v-text-field v-model="nuevoItem.body" label="Contenido"></v-text-field>
        <v-text-field v-model="nuevoItem.completed" label="Completado"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="agregarItem">Agregar</v-btn>
        <v-btn color="error" @click="cerrarDialogoAgregarItem">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogActualizarItem" max-width="500">
    <v-card>
      <v-card-title>Actualizar item</v-card-title>
      <v-card-text>
        <v-text-field v-model="itemActualizable.id" label="Id"></v-text-field>
        <v-text-field v-model="itemActualizable.title" label="Title"></v-text-field>
        <v-text-field v-model="itemActualizable.body" label="Body"></v-text-field>
        <v-text-field v-model="itemActualizable.completed" label="Completado"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="actualizarItem(itemActualizable.id)">Actualizar</v-btn>
        <v-btn color="error" @click="cerrarDialogoActualizarItem">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogBorrarItem" max-width="500">
    <v-card>
      <v-card-title>Borrar Iitem por ID</v-card-title>
      <v-card-text>
        <v-text-field v-model="itemABorrar.id" label="Id"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="primary" @click="borrarItem(itemABorrar.id)">Eliminar item</v-btn>
        <v-btn color="error" @click="cerrarDialogoBorrarItem">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="dialogNoExisteID" max-width="500">
    <v-card>
      <v-card-title>Ese id no existe</v-card-title>
    </v-card>
    <v-card-actions>
      <v-btn color="primary" @click="cerrarDialogoIdNoEncontrado">Cerrar</v-btn>
    </v-card-actions>
  </v-dialog>
</template>

<script>
export default {
  data() {
    return {
      search: '',
      frases: [],
      headers: [
        { text: 'ID', value: 'id' },
        { text: 'Título', value: 'title' },
        { text: 'Cuerpo', value: 'body'},
        { text: 'Completado', value: 'completed' }
      ],
      dialogAgregarItem: false,
      dialogActualizarItem: false,
      dialogBorrarItem: false,
      dialogNoExisteID: false,
      nuevoItem: {
        title: '',
        body: '',
        completed: '',
      },
      itemActualizable: {
        id: '',
        title: '',
        body: '',
        completed: '',
      },
      itemABorrar: {
        id: '',
      }
    };
  },
  async mounted() {
    try {
      const storedFrases = localStorage.getItem('frases');
      
      if (storedFrases) {
        this.frases = JSON.parse(storedFrases);
      } else {
        console.log("Obtenido frases del servidor");
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        if (!response.ok) {
          throw new Error('Error al obtener datos');
        }
        this.frases = await response.json();
      }
    } catch (error) {
      console.error('Error al obtener datos:', error);
    }
  },
  methods: {
    abrirDialogoAgregarItem() {
      this.dialogAgregarItem = true;
    },
    cerrarDialogoAgregarItem() {
      this.dialogAgregarItem = false;
    },
    abrirDialogoActualizarItem() {
      this.dialogActualizarItem = true;
    },
    cerrarDialogoActualizarItem() {
      this.dialogActualizarItem = false;
    },
    abrirDialogoBorrarItem() {
      this.dialogBorrarItem = true;
    },
    cerrarDialogoBorrarItem() {
      this.dialogBorrarItem = false;
    },
    abrirDialogoIdNoEncontrado() {
      this.dialogNoExisteID = true;
    },
    cerrarDialogoIdNoEncontrado() {
      this.dialogoNoExisteId = false;
    },
    async agregarItem() {
      try {
        const nuevoId = this.frases.length + 1;
        const res = await fetch('https://jsonplaceholder.typicode.com/posts', {
          method: 'POST',
          body: JSON.stringify({
            id: nuevoId,
            title: this.nuevoItem.title,
            body: this.nuevoItem.body,
            completed: this.nuevoItem.completed,
            userId: 1
          }),
          headers: {
            'Content-type': 'application/json; charset=UTF-8'
          }
        });
        if (!res.ok) {
          throw new Error('Error al crear el item');
        }
        const json = await res.json();

        this.frases.push({
          ...json,
          id: nuevoId
        });
        localStorage.setItem('frases', JSON.stringify(this.frases));
        this.cerrarDialogoAgregarItem();
      } catch (error) {
        console.error('Error al crear el item:', error);
      }
    },
    async actualizarItem(id) {
  try {
    // Busca el índice del elemento que se va a actualizar
    var index = -1;
    for (let i = 0; i < this.frases.length; i++) {
      if (this.frases[i].id == id) {
        index = i;
      }
    }

    if (index === -1) {
      throw new Error('No se encontró el elemento con el ID especificado');
    }

    // Realiza la solicitud PUT para actualizar el elemento en el servidor
    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
      method: 'PUT',
      body: JSON.stringify({
        id: this.itemActualizable.id,
        title: this.itemActualizable.title,
        body: this.itemActualizable.body,
        completed: this.itemActualizable.completed,
        userId: 1
      }),
      headers: {
        'Content-type': 'application/json; charset=UTF-8'
      }
    });

 //   if (!res.ok) {
   //   throw new Error('Error al actualizar el item en el servidor');
  //  }

    // Actualiza el elemento en el array frases
    this.frases[index] = {
      id: this.itemActualizable.id,
      title: this.itemActualizable.title,
      body: this.itemActualizable.body,
      completed: this.itemActualizable.completed
    };

    // Actualiza el almacenamiento local
    localStorage.setItem('frases', JSON.stringify(this.frases));

    // Cierra el diálogo de actualizar item
    this.cerrarDialogoActualizarItem();
  } catch (error) {
    console.error('Error al actualizar el item:', error);
  }
},
  async borrarItem(id) {
  try {
    var index = -1;
    for (let i = 0; i < this.frases.length; i++) {
      if (this.frases[i].id == id) {
        index = i;
      }
    }

    if (index === -1) {
      throw new Error('No se encontró el elemento con el ID especificado');
    }

    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
      method: 'DELETE',
    });

    if (!res.ok) {
      throw new Error('Error al borrar el item');
    }

    this.frases.splice(index, 1);
    localStorage.setItem('frases', JSON.stringify(this.frases));
    this.cerrarDialogoBorrarItem(); // Corregido
  } catch (error) {
    console.error('Error al borrar el item:', error); // Corregido
  }
}
  }
};
</script>
